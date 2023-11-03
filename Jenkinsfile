node {
    stage('Build') {
        docker.image('python:2-alpine').inside{
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        try{
            docker.image('qnib/pytest').inside{
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
        }
        catch(e){}
        finally{
            junit 'test-reports/results.xml'
        }
    }
    stage('Deploy'){
        try{
            def dockerImage = 'cdrx/pyinstaller-linux:python2'
            def sourceFile = 'sources/add2vals.py'
            def targetDirectory = 'dist/add2vals'

            def delivered = docker.image(dockerImage).inside {
                sh "pyinstaller --onefile ${sourceFile}"
            }

            if (delivered == 0) {
                archiveArtifacts allowEmptyArchive: true, artifacts: targetDirectory
            } else {
                currentBuild.result = 'FAILURE'
            }
        }
        catch(e){}
        // finally{
        //     // archiveArtifacts 'dist/add2vals'
        //     sh 'ls / -al'
        //     // archiveArtifacts artifacts: 'dist/add2vals'
        // }
    }
}
