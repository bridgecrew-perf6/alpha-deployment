pipeline {
  agent any
    
  tools {nodejs "node"}
    
  stages {

    stage ('Prepare destination host') {
      steps {
        sh '''
          ssh -T centos@192.168.231.144 << ENDSSH
          sudo rm -rf /home/centos/aplha*
          sudo sleep 2
          cd /home/centos
          sudo mkdir cd /home/centos/aplha/
          sudo chmod +x /alpha
          cd /home/centos/aplha
          sudo mkdir /deploy1 /deploy2 /deploy3
          sudo dnf install npm -y
          sudo npm install
ENDSSH
      '''
      }
    }

    stage('checkout SCM') {
      steps {
        git 'https://github.com/bhimra/alpha-deployment.git'
        sh 'pwd && sudo chmod -R 775 .'
      }
    }

    stage('SCP application on /locations') {
      steps {
        sh 'scp index.js centos@192.168.231.144:/home/centos/aplha/deploy1'
        sh 'scp index2.js centos@192.168.231.144:/home/centos/aplha/deploy2'
        sh 'scp index3.js centos@192.168.231.144:/home/centos/aplha/deploy3'
        sh 'echo "All deployment folder are READY."'
      }
    }   


    stage('ArchiveArtifacts'){
            steps{
                archiveArtifacts artifacts: '**', followSymlinks: false
            }
        }
    
    stage ('Verify node service') {
      steps {
        sh '''
          ssh -t -t  centos@192.168.231.144 'bash -s << 'ENDSSH'
          if [[ Z=$(sudo ps aux | grep -i [n]ode | awk 'NR==1' | gawk {'print $2'}) ]];
          then
              sudo kill -9 $Z
              echo "node service stop successfully."
              sudo pkill node
              exit 0
          else
              echo "node service failed"
          fi
ENDSSH'
       '''
       }
    }


    stage ('Start the index.js node service') {
      steps {
        sh '''
        set -x
        ssh centos@192.168.231.144 "
                    cd /home/centos/aplha/deploy1
                    node index.js > /dev/null 2>&1 <&- & "
        sudo sleep 3                    
        X=$(curl -k  -o /dev/null -s -w %{http_code} http://192.168.231.144:3000)
        if [ $X -eq 200 ];
            then
                echo -e 'web site on port 3000 is running'
            else
                echo -e 'web site on port 3000 is down' 
        fi '''
       }
     }

    stage ('Start the index2.js node service') {
      steps {
        sh '''
        set -x
        ssh centos@192.168.231.144 "
                    cd /home/centos/alpha/deploy2
                    node index2.js > /dev/null 2>&1 <&- & "
        sudo sleep 3                    
        X=$(curl -k  -o /dev/null -s -w %{http_code} http://192.168.231.144:3002)
        if [ $X -eq 200 ];
            then
                echo -e 'web site on port 3002 is running'
            else
                echo -e 'web site on port 3002 is down' 
        fi '''
       }
     }

    stage ('Start the index3.js node service') {
      steps {
        sh '''
        set -x
        ssh centos@192.168.231.144 "
                    cd /home/centos/alpha/deploy3
                    node index3.js > /dev/null 2>&1 <&- & "
        sudo sleep 5                    
        X=$(curl -k  -o /dev/null -s -w %{http_code} http://192.168.231.144:3003)
        if [ $X -eq 200 ];
            then
                echo -e 'web site on port 3003 is running'
            else
                echo -e 'web site on port 3003 is down' 
        fi '''
       }
     }
  }  
}
