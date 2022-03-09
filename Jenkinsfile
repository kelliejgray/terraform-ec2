pipeline {
    agent any
	
	parameters {
	    string(name: 'environment', defaultValue: 'terraform', description: 'Workspace/environment fileto use for deployment')
		booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
           }
	
	
	environment {
	   AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
	   AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
	   }
	   
    stages {
        stage('checkout') {
            steps {
                 script{
                        dir("terraform")
                        {
                            git "https://github.com/kelliejgray/terraform-ec2.git"						
	                    }
					}
				}
            }  	
	
	    stage('plan') {
		    steps {
			    sh 'pwd;cd terraform/terraform-ec2 ; terraform init=false'
				sh 'pwd;cd terraform/terraform-ec2 ; terraform workspace new ${environment}'
				sh 'pwd;cd terraform/terraform-ec2 ; terraform workspace select ${environment}'
				sh 'pwd;cd terraform/terraform-ec2 ; terraform plan -input=false -out=tfplan'
				sh 'pwd;cd terraform/terraform-ec2 ; terraform show -no-color tfplan > tfplan.txt'
			}
		}
		stage('Approval') {
		   when {
		       not {
			       equals expected: true, actual: params.autoApprove
				}
			}
			
			steps {
			    script {
				    def plan = readFilie 'terraform/terraform-ec2/tfplan.txt'
					imput message: "Do you want to apply the plan",
					parameters: [text(name: 'plan', description: 'please review the plan', defaultValue: plan)]
				}
			}
		}
		
		stage('Apply') {
		    steps {
			     sh "pwd;cd terraform/terraform-ec2 ; terraform apply -input=false tfplan"
			}
		}
	}
	
}
				
