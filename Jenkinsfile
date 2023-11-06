node {
    stage('Build') {
        docker.image('python:3-alpine').inside{
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
            docker.image('cdrx/pyinstaller-linux:python3').run("--name submission-2").inside{
                sh 'pyinstaller --onefile sources/add2vals.py'
                archiveArtifacts allowEmptyArchive: true, artifacts: 'dist/add2vals'
                sh 'ls dist'
            }
        }
        catch(Exception e){
            print e
        }
    }
}
