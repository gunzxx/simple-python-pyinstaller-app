node {
    stage('Build') {
        docker.image('python:3.12.0-alpine3.18').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
        }

        try{}
        catch(e){}
        finally{
            junit 'test-reports/results.xml'
        }
    }

    stage('Manual Approval') {
        input "Lanjutkan ke tahap Deploy?"
    }

    stage('Deploy') {
        agent any

        environment {
            VOLUME = pwd() + '/sources:/src'
            IMAGE = 'cdrx/pyinstaller-linux:python2'
        }

        script {
            sleep time: 60, unit: 'SECONDS'
        }

        dir(path: env.BUILD_ID) {
            unstash(name: 'compiled-results')
            sh "docker run --rm -v ${VOLUME} ${IMAGE} pyinstaller -F add2vals.py"
        }

        try{}
        catch(e){}
        finally{
                archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                sh "docker run --rm -v ${VOLUME} ${IMAGE} rm -rf build dist"
        }
    }
}
