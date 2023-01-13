
# ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATE PROJECT 7 TO 10

## INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE
- Updated Name tag on my Jenkins EC2 Instance to Jenkins-Ansible
- Created an 'ansible-config-mgt' repository on my Github account
![1](https://user-images.githubusercontent.com/69347415/212208990-642945c6-43aa-4d69-8d21-c0c734903be4.jpg)
- Updated Ubuntu repository index and installed Ansible with the command below
```
sudo apt update
sudo apt install ansible
```
![4](https://user-images.githubusercontent.com/69347415/212209067-37b2bf49-ab1f-461a-84da-c4d4736e3871.jpg)
- ran `ansible --version` to check the ansible version
- Created a new Freestyle project 'ansible' in Jenkins and pointed it to my previously created ‘ansible-config-mgt’ repository.
- Configured Webhook in GitHub and set webhook to trigger ansible build.
- Then configured a Post-build job to save all (**) files in the repository
![3](https://user-images.githubusercontent.com/69347415/212209218-c9f9cb1e-1f63-4b93-b97f-918855cd8528.jpg)
- Tested my setup by making some changes in README.MD file in master branch
- Made sure that builds starts automatically and Jenkins saves the build artifacts
![6](https://user-images.githubusercontent.com/69347415/212209384-7b4ff85e-ccda-4641-ae3f-7348fec3970a.jpg)
- Set up my IDE to connect to remote server using 'Remote Protocol' extension as well as to my Github repository
- Installed git and cloned down my ansible-config-mgt repo to my Jenkins-Ansible instance
![8](https://user-images.githubusercontent.com/69347415/212209535-6adcb886-fd6e-4da7-82b7-a67bb567aecf.jpg)

## BEGIN ANSIBLE DEVELOPMENT
- Created and checkout into a new 'feature/prj-11' branch in my ansible-config-mgt GitHub repository.
- Created a playbooks and inventory directory in the feature branch. Playbooks to store all playbooks files and Inventory to store and organize hosts
- Created a common.yml playbook in the playbooks folder
- Created a dev.yml, staging.yml, uat.yml and prod.yml files in the inventory folder
- Configured ssh-agent to ssh into my Jenkins-Ansible server. This is very much required by ansible to ssh into the target servers
- Updated inventory/dev.yml file with this snippet of code below
![10](https://user-images.githubusercontent.com/69347415/212209779-e5a4303a-9433-4034-b6ec-c74771f84e0a.jpg)

## CREATE A COMMON PLAYBOOK
- Updated my playbooks/common.yml file with following playbooks below for Ansible
![11](https://user-images.githubusercontent.com/69347415/212209830-cfe68990-00d8-45c0-a196-d257cf2c6aea.jpg)
- Saved all changes made on my local machine and got them commited to Git to push to upstream repository with the code snippet below
```
git status
git add .
git commit -m "commit message"
```
- Raised a Pull Request (PR), Put on another developer hat and peer-reviewed my branch. Then merged to the master branch.
- After the PR was merged, Jenkins did its job and saved all the files (build artifacts) to `/var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/` directory on the Jenkins-Ansible server
![13](https://user-images.githubusercontent.com/69347415/212209924-d33fe6c3-cf0b-4a8b-b4fe-1d2516e527e4.jpg)
![12](https://user-images.githubusercontent.com/69347415/212209963-dfbd347f-9d94-410e-9816-c0c15402a608.jpg)
![14](https://user-images.githubusercontent.com/69347415/212210018-c12b8198-5725-41f2-90e9-e107409b2f5a.jpg)

## RUN FIRST ANSIBLE TEST
- Executed ansible-playbook command from the Jenkins-Ansible server and verified if the playbook actually works with the command below
`ansible-playbook -i ansible-config-mgt/inventory/dev.yml playbooks/common.yml`
![16](https://user-images.githubusercontent.com/69347415/212210127-dac0ce92-dc33-419f-acb5-09730d0d256c.jpg)
- Initially I got series of error as seen below. Went online to debug and had to configure and disable a few defailt ansible configurations by creating an overridding ansible.cfg file. These configuratiosn include a path to private_ssh_file and disabling host_key_checking as seen below
![18](https://user-images.githubusercontent.com/69347415/212210198-4417d734-96ce-4d4d-84dd-c332ac90ca15.jpg)
- re-ran the ansible-playbook command and then got successful response from the execution as seen below
![21](https://user-images.githubusercontent.com/69347415/212210244-ccab7a83-2e8f-40b1-aa36-fffed929d12a.jpg)
- then I ssh into each target-server to confirm for successful execution of the playbooks as expected by ansible
![23](https://user-images.githubusercontent.com/69347415/212210292-42c2818e-5f9f-4bd9-b0eb-fd7473a6810a.jpg)
![22](https://user-images.githubusercontent.com/69347415/212210296-620a5da3-504b-4ab5-b95c-44c92a0ded6e.jpg)
