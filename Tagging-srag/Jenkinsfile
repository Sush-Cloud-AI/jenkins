// promotion stratergy 

pipeline {
    agent any
    parameters{

            string(name: 'COMPONENT', defaultValue: 'mongodb', description: 'Enter the name of component.') // supplying value for ENV and COMPONENT in the cammand to parmetrise it.
            choice(name: 'ENV', choices: ['dev', 'prod'], description: 'Choose the environment')
    }

    environment {                   // declaring env variables

        SSH_CRED = credentials('ssh-centos7') // masking the credentials using jenins secret manager .
        SGIT = credentials('github-token')
    }

    stages {
        stage('Lint check'){  // will run only against any feature branch 
            when { branch pattern: "feature-.*", comparator: "REGEXP"}  // use . befor *  for regex missing in official doc of jenkins
            steps{
                sh "env"
                sh " echo style check and running in feature branch"
            }
        }



        stage('dry-run'){ // will run against only hen PR is rasied . can check in pr tab in jenkins
            when { branch pattern: "PR-.*", comparator: "REGEXP"}  // use . befor *  for regex missing in official doc of jenkins
            steps {
                sh "env" // to see the env variables for ssh credential keys to use it in ansible user and passowrd in the command to run ansible 
                sh "ansible-playbook robo-ec2.yml -e ansible_user=${SSH_CRED_USR} -e ansible_password=${SSH_CRED_PSW} -e COMPONENT=${params.COMPONENT} -e ENV=${params.ENV}"  // commansd to run the dry run ec2 and destroy 
            }
        }

        stage('Tagging'){
            when{ branch 'main' } // if the branch is main . in automatic tagging  the tags are pushed 
            steps{
                
                git branch: 'main', url: "https://${SGIT_USR}:${SGIT_PSW}@github.com/Sush-Cloud-AI/ansible.git"   // the git is cloned for the girt to clone. 
                sh "bash -x auto-tag.sh"  // this is the code to auto increment the tag and push it 
            }
        }


        
        //stage('Running on tags'){
        //    when{ expression {env.TAG_NAME != null}} // will run when a tag is pushed . Tags are only pushed in main branch and this stage will run 
        //    steps{    
        //        sh " echo runs when you push a git tag"
        //    }
        //}
        
    }
}

