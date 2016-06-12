properties(
	[
		[
			$class: 'jenkins.model.BuildDiscarderProperty', strategy: [$class: 'LogRotator', numToKeepStr: '10', artifactNumToKeepStr: '10']
		]
	]
)

stage "Build"

node {
	checkout scm
	sh "buildid -n"
	sh "make"

	stash includes: 'dist/*.zip', name: 'binzip'
}

stage "Package & Publish"

parallel (
	rpmFedora: { node { ws {
		writeFile file: 'README.txt', text: "Fedora"
		unstash "binzip"

		mv dist SOURCES
		mkdir SPECS
		unzip -jo SOURCES/buildid.zip "buildid/var/buildid.spec" "buildid/.buildid" -d SPECS/
		buildid -f rpmmacro > SPECS/buildid.rpmmacro
		rpmbuild -ba SPECS/buildid.spec 
	}}}, 

	rpmEl6: { node { ws {
		writeFile file: 'README.txt', text: "EL6"
		unstash "binzip"
		sleep 10
	}}}, 
	
	rpmEl7: { node { ws {
		writeFile file: 'README.txt', text: "EL7"
		unstash "binzip"
		sleep 10
	}}}, 

	failFast: true
)
