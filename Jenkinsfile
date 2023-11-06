node {
    try {
        stage('Build') {
            def buildDockerImage = docker.image('python:3.12.0-alpine3.18')
            buildDockerImage.inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }

        stage('Test') {
            def testDockerImage = docker.image('qnib/pytest')
            testDockerImage.inside {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            junit 'test-reports/results.xml'
        }

        stage('Manual Approval') {
            input "Lanjutkan ke tahap Deploy?"
        }

        stage('Deploy') {
            def VOLUME = pwd() + '/sources:/src'
            def IMAGE = 'cdrx/pyinstaller-linux:python2'

            sleep time: 60, unit: 'SECONDS'
            
            def deployDockerImage = docker.image(IMAGE)
            deployDockerImage.inside("-v ${VOLUME}") {
                unstash(name: 'compiled-results')
                sh "pyinstaller -F add2vals.py"
            }

            archiveArtifacts "sources/dist/add2vals"
            sh "rm -rf build dist"
        }
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        cleanWs()
    }
}
