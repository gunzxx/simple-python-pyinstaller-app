node {
    stage('Build') {
        docker.image('python:2-alpine').inside{
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside{
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        try{}
        catch(e){}
        finally{
            junit 'test-reports/results.xml'
        }
    }
    stage('Deploy'){
        docker.image('cdrx-pyinstaller-linux:python2').inside{
            sh 'pyinstaller --onefile sources/add2vals.py'
        }
        try{}
        catch(e){}
        finally{
            archiveArtifacts 'dist/add2vals' 
        }
    }
}
