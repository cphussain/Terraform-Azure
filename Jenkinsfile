
pipeline {

    parameters {
        string(name: 'environment', defaultValue: 'terraformenv', description: 'Workspace/environment file to use for deployment')
        booleanParam(name: 'autoApprove', defaultValue: true, description: 'Automatically run apply after generating plan?')

    }


     environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')

        
    }

   agent  any
        options {
                timestamps ()
                ansiColor('xterm')
            }
        tools{
            terraform 'Terraform'
            }
    stages {
        
        stage('Checkout') {
            steps {
                 script{
                        dir("terraformDir")
                        {
                            git (
                                branch: 'main',
                                url: 'https://github.com/cphussain/Terraform-Azure.git'
                                
                            )
                        }
                    }
                }
            }


        stage('Plan') {
            steps {
               script{
                        
                            
                            bat 'terraform --version'
                            bat 'terraform init -input=false'
                            bat 'terraform workspace new %environment%'
                            bat 'terraform workspace select %environment%'
                            bat "terraform plan -input=false -out tfplan "
                            bat 'terraform show -no-color tfplan > tfplan.txt'
                            bat 'more tfplan.txt'
                        
               }
            }
        }
        stage('Approval') {
           
           steps {
               script {
                  
                        bat 'set /p plan=<tfplan.txt'
                        input(id: "Deploy Gate", message: "Do you want to apply the plan? ", ok: 'Apply')
                        parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                   
               }
           }
       }
        stage('Apply') {
            steps { script{
                        dir("aws/CreateEC2")
                        {
                bat "terraform apply -input=false tfplan"
                        }}
            }
        }
    }

  }


