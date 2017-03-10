# Base cookbook `attributes/default.rb
```ruby
default["training"]["deploy_version"] = "0.0.74"
default["training"]["workspace"] = "/home/vagrant"
```

# Enviroment file

```json
{
    "name": "dev",
    "description": "",
    "cookbook_versions": {
        "base": "= 0.1.4",
        "security": "~> 0.1",
        "monitoring": "~> 0.1",
        "docker-deploy": "~> 0.1"
    },
    "json_class": "Chef::Environment",
    "chef_type": "environment",
    "default_attributes": {
        
    },
    "override_attributes": {
        
    }
}
```

# Jenkins

### Active Choise Parametr `environment`
```groovy
"knife environment list".execute().text.tokenize()
```

### Active Choise Parametr `role`
```groovy  
"knife role list".execute().text.tokenize()
```

### Dynamic Parametr `artifactVersion`
```groovy
artifacts = """ curl --insecure \
                     --url https://builder:5000/v2/webapp/tags/list \
            """.execute().text
parser = new groovy.json.JsonSlurper()
list = parser.parseText artifacts
list.tags.last()
```
### Pipeline
```groovy
def gitUrl = { credentials ->
	credentials = credentials ? "$credentials@" : ""
	"https://${credentials}github.com/JugosD/chef-repo.git"
}

cookbook            = "base"
attributesFileName  = "cookbooks/$cookbook/attributes/default.rb"
metadataFileName    = "cookbooks/$cookbook/metadata.rb"
environmentFileName = "environments/${environment}.json"

@NonCPS
def updateAttr(text, version) {
	def expr = /(.*training.*deploy_version.*=\s*)"([0-9.]+)"/
	text = text.readLines().collect {
		(it =~ expr).each {match, header, version_text ->
			it = "${header}\"${version}\""
		}
		it
	}.join("\n")
}

@NonCPS
def updateMeta(text) {
	def expr = /(\s*version\s+)"([0-9.]+)"/
	def version
	text = text.readLines().collect {
		(it =~ expr).each {match, header, version_text ->
			def (major, minor, build) = version_text.split("[.]")
			version = "${major}.${minor}.${(build as Integer) + 1}"
			it = "${header}\"${version}\""
		}
		it
	}.join("\n")
	[text: text, version: version]
}

@NonCPS
def updateEnv(text, version) {
	def env = new groovy.json.JsonSlurper().parseText(text)
	env.cookbook_versions[cookbook] = "= $version"
	def json = groovy.json.JsonOutput.toJson(env)
	groovy.json.JsonOutput.prettyPrint(json)
}

node("master") {
	stage("Preparation") {
		git gitUrl()
	}
	stage("Update versions") {
		// update artifact version
		def attrText = updateAttr(readFile(attributesFileName), artifactVersion)
		println attrText
		writeFile file: attributesFileName, text: attrText
		// increase version of cookbook
		def metadata = updateMeta(readFile(metadataFileName))
		println metadata.text
		writeFile file: metadataFileName, text: metadata.text
		// update cookbook version in environment file
		def envText = updateEnv(readFile(environmentFileName), metadata.version)
		println envText
		writeFile file: environmentFileName, text: envText
	}
	stage("Update environment and $cookbook cookbook") {
		sh "knife environment from file $environmentFileName"
		sh """knife cookbook \
			upload $cookbook \
			--cookbook-path cookbooks
		"""
	}
	stage("Execute chef-client") {
		withCredentials(
			[usernamePassword(
				credentialsId: 'knifeSSH',
				usernameVariable: 'user',
				passwordVariable: 'pass')]) {
			sh """knife ssh \
				'role:$role AND chef_environment:$environment' \
				chef-client \
				-x $user \
				-P $pass \
			"""
		}
	}
	stage("Publish") {
		withCredentials(
			[usernameColonPassword(
				credentialsId: "GithubCredential",
				variable: "credentials")]) {
			sh """set -x \
				&& git \
					-c user.name='Jenkins' \
					-c user.email='without@email.com' \
					commit -am 'Jenkins $artifactVersion' \
				&& git push ${gitUrl(credentials)} master\
			"""
		}
	}
}
```