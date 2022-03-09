pipeline {
    
	
	parameters {
	    string(name: 'enviroment', defaultValue: 'terraform', description: 'Woorkspace/enviroment fileto use for deployment')
		booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')

	}
	
	
	enviroment {
	   AWS_ACCESS_KEY_ID       = credentials('AWS_ACCESS_KEY_ID')
	   AWS_SECRET_ACCESS_KEY   = credentials('AWS_ SECRET_ACCESS_KEY')
	   }
	   
	agent any
         options {
                 timestamp ()
                 ansiColor('xterm')
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
			    sh 'pwd;cd terraform/aws-instance-first-script ; terraform init=false'
				sh 'pwd;cd terraform/aws-instance-first-script ; terraform workspace new ${enviroment}'
				sh 'pwd;cd terraform/aws-instance-first-script ; terraform workspace select ${enviroment}'
				sh 'pwd;cd terraform/aws-instance-first-script ; terraform plan -input=false -out=tfplan
				sh 'pwd;cd terraform/aws-instance-first-script ; terraform show -no-color tfplan > tfplan.txt
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
					parameters: [text(name: 'plan', description: 'please review the paln', defaultValue: plan)]
				}
			}
		}
		
		stage('Apply') {
		    steps {
			     sh "pwd;cd terraform/aws-instance-first-script ; terraform apply -input=false tfplan"
			}
		}
	}
	
}
				
