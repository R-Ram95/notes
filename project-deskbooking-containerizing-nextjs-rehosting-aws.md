- Background> [!important]  
    > BackgroundT3 stack using:Clerk - authTRPC - to make the APIPrisma - ORMPlanetScale - SQL DBWant to run Desk app in Docker to utilize AWS RDS or DynamoDB ⇒ requires running App in Docker Container, DB in Docker Container, all on ECS  
    
- Deployment on AWS - ECS> [!important]  
    > Deployment on AWS - ECSTermsTask - Docker ContainerTask Definition - details for task to run (vcpu required, memory required)charged per vCPU and Memory requested per taskneed to add inbound rule to security group for ECS ServiceRolling deployments: need to set min or max health capacity so that ECS can add and remove tasks to update to new task ⇒ CloudFormation update will get stuck if we do not specify min or maxmust use the awsvpc networking mode for Fargate ⇒ this means that each task gets an ENI:![[/Untitled 26.png|Untitled 26.png]]ECS with Fargate launch type creates an ENI for each task, this gives each task network isolation Host port (ENI Port) = Container port (Task Port)i.e. Connections to Port 80 on the ENI are forwarded to port 80 on the containersecurity group port inbound rule for the ENI must include the Docker Container port, i.e. connections to <ipaddress>:<port> = port for inbound rule of security group = container porthttps://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking-awsvpc.htmlhttps://docs.aws.amazon.com/AmazonECS/latest/userguide/fargate-task-networking.htmlUsing ECS Fargate Service with Load Balancer:![[/Untitled 1 15.png|Untitled 1 15.png]]  
    
- Using ECS Exec to Access Container on ECS> [!important]  
    > Using ECS Exec to Access ContainersECS Exec allows users to initiate an interactive session with a container (the equivalent of a docker exec -it ) whether in a shell or via a single command.  
    How it worksRuns using the SSM Session Manager to create a secure connection between your machine (i.e. laptop) and the task running on ECS. SSM Agent binaries are bind mounted into the containers. The SSM Agent does not run as a side car but rather as an additional process on the container.  
    Client Side Requirements:need SSM Session Manager Plugin for the AWS CLIServer-side Requirments(Fargate):nothing you need to do b/c Fargate already includes the software requirements  
    Configuring the task role with the proper IAM policyECS task needs to have the proper IAM privileges for the SSM core agent to call the SSM serviceif logging is added, the Task Role needs IAM permissions to log output to S3 or CloudWatch  
    Reference:https://aws.amazon.com/blogs/containers/new-using-amazon-ecs-exec-access-your-containers-fargate-ec2/  
    
- Docker File> [!important]  
    > Docker FileNextJS requires different process for build-time(client side, i.e. FE) and run-time environment (server-side, i.e. BE) variables:SS variables should be in the environment section of the docker-compose.ymlCS variables should be passed in as ARGS in the Dockerfilehttps://create.t3.gg/en/deployment/dockerset up dev environment using docker-composewe can use the MakeFile to run the command we need to build the image and spin up the app and DB containersneed a script to seed the db though  
    
- Useful Docker Commands> [!important]  
    > Useful Docker CommandsCLI for mysql dbdocker exec -it desk_booking_database mysql -u root -pGet container ip address:docker inspect --format='{{range .NetworkSettings.Networks}}{{.MacAddress}}{{end}}' $INSTANCE_IDGet Port Bindings: docker inspect --format='{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' $INSTANCE_IDLook at docker services:docker inspect  
    DB Queries`USE desk_booking_database;`  
      
    `show tables;`  
      
    `SELECT * from DeskBooking;`  
      
    `SELECT * from Desk;`  
      
    `SELECT * from Monitor;`8  
    DB URL:DATABASE_URL='mysql://root:super-secret-password@38b57c0d620383aebd279924fd5b6ef6b0ec4d7bddfd20b81021217d411a84338b57c0d620383aebd279924fd5b6ef6b0ec4d7bddfd20b81021217d411a843ee:3306/desk_booking_database'  
    
- Privileged Ports - Unix> [!important]  
    > Privileged Ports - UnixA Linux security feature where non-root users are not allowed to run servers on ports 1-1023. This is to ensure that when you connect to a service on one of these ports, you can be sure it is a real port and not one set up by a hacker.https://www.w3.org/Daemon/User/Installation/PrivilegedPorts.htmlhttps://www.baeldung.com/linux/bind-process-privileged-port#:~:text=In Linux%2C a privileged port,bound to non-root processes.&text=In this example%2C we use,listen on (port 80).Option 1 - Using setcap setcap 'cap_net_bind_service=+ep' /path/to/programgrants permanent access to a specific binary (located at path/to/program)capability is CAP_NET_BIND_SERVICE+ep means effective and permittednot ideal because it means any process started using the binary will run with the effective and permitted capabilities, i.e. if you apply the above command to the node binary, any process started with node will have the effectice and permitted capabilities❕This does not work on the Fargate launch type because the AWS Fargate only supports the SYS_PTRACE capability.https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_KernelCapabilities.html  
    Option 2 - using authbind1) install authbind2) run:sudo touch /etc/authbind/byport/80  
    sudo touch /etc/authbind/byport/443  
    sudo chmod 777 /etc/authbind/byport/80  
    sudo chmod 777 /etc/authbind/byport/4433) execute command with authbindauthbind --deep /path/to/binary command line argsit grants trust to the user/group and provides per port accessOption 3 - Redirect connections to port < 1023you redirect connection on port 80 for example to some other port you open as a normal user1) run as root:// use to redirect traffic before it reaches it reaches the local process  
    # iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080  
      
    /// use to redirect locally generated traffic  
    # iptables -t nat -I OUTPUT -p tcp -d 127.0.0.1 --dport 80 -j REDIRECT --to-ports 8080to delete the rule:  
    // print the rules  
    # iptables -t nat --line-numbers -n -L  
    // delete the desirect rule  
    # iptables -t nat -D PREROUTING 2https://coderwall.com/p/plejka/forward-port-80-to-port-3000Port forwarding with ufwhttps://serverfault.com/questions/238563/can-i-use-ufw-to-setup-a-port-forward  
    
- Errors> [!important]  
    > Prisma Client Could Not Locate the Query Engine for RunTimePrisma Client could not locate the Query Engine for runtime "linux-musl-arm64-openssl-1.1.x".  
    desk-booking |  
    desk-booking | This happened because Prisma Client was generated for "darwin-arm64", but the actual deployment required "linux-musl-arm64-openssl-1.1.x".  
    desk-booking | Add "linux-musl-arm64-openssl-1.1.x" to `binaryTargets` in the "schema.prisma" file and run `prisma generate` after saving it:  
    desk-booking |  
    desk-booking | generator client {  
    desk-booking | provider = "prisma-client-js"  
    desk-booking | binaryTargets = ["native", "linux-musl-arm64-openssl-1.1.x"]  
    desk-booking | }https://www.prisma.io/docs/concepts/components/prisma-schema/generators\#binary-targetsPrisma Client JS (prisma-client-js) uses several engines. Engines are implemented in Rust and are used by Prisma in the form of executable, platform dependent engine files. Depending on which platform you are executing your code on, you need the correct file. "Binary targets" are used to define which files should be present for the target platform(s)We need to make sure that the binary for the engine is compatible (x86 vs. ARM) for the Docker platform. When running locally, this means that we need Docker to use an Emulator if the target platform is x86 but your machine is ARM.  
      
    > [!important]  
    > DeskApp running on ECS keeps exiting with 1Because we are trying to bind a non-root process (Desk booking app running on port 80) to a privileged port on Linux (ports 1-1023).Error: listen EACCES: permission denied 0.0.0.0:80  
    
      
    
- DynamoDB> [!important]  
    > DynamoDB Pricingcharged for reading, writing, storing data in DynamoDB tableOn-demand and Provisioned CapacityPrisma does not support DynamoDB: https://www.prisma.io/docs/orm/reference/supported-databasesKey TermsRead Request Units: units for billing api reads from dbStrongly Consistent - 1 RRU for data up to 4KBEventually Consistent - 1/2 RRU for data up to 4KBTransactional - 2 RRU for data up to 4KBWrite Request Unit: units for billing api writes to dbStandard: 1 WRU for data up to 1KBTransactional: 2 WRU for data up to 1KBReplicated Write Request Unit: for Global tables, data is automatically written to multiple Regions of your choice.Streams read request unit: GetRecords api call to DynamoDB stream ⇒ each request returns 1MB of dataTransactional read/write requests: Transactional read/writes guarantee all operations within a single transaction succeed or fail as a setDynamoDB Table Classes: Standard, Standard-IACosts:Throughput - Standard Table Class:WRU = $1.25/million request unitsRRU = $0.25/million request unitsThroughput - Standard-IA Table Class:WRU = $1.56/million request unitsRRU = $0.31/million request unitsStorage - Standard:first 25GB-month free, $0.25/GB-month afterStorage - Standard-IA:$0.10/GB-month  
    Size of Data:![[/Untitled 2 14.png|Untitled 2 14.png]]![[/Untitled 3 14.png|Untitled 3 14.png]]  
    
- NextJS on AWS> [!important]  
    > NextJS On AWS  
    Where do the “back end” api calls run when deployed on AWS? i.e. where do methods in the /src/pages/api run?On Amplify:run on Lambda@Edge ⇒ this is a Public APIMiddleware can be added to secure routes ⇒ e.g. with tRPC and Clerk, middleware is added that check for authorized usershttps://clerk.com/docs/references/nextjs/auth-middleware// THIS CODE IS IN /src/server/api/trps.ts  
    const enforceUserIsAuthed = t.middleware(async ({ ctx, next }) => {  
    if (!ctx.currentUserId) {  
    throw new TRPCError({ code: "UNAUTHORIZED", message: "Not logged in" });  
    }  
      
    return next({  
    ctx: {  
    currentUserId: ctx.currentUserId,  
    },  
    });  
    });  
      
    export const privateProcedure = t.procedure.use(enforceUserIsAuthed);On ECS:run in the ECS Task  
    
      
    
- DB Deployment on AWS - Self Hosted EC2
    
    ==This option is harder than RDS and probably not worth it as RDS may be cheaper==
    
    > [!important]  
    > Connecting to Deployed EC2 in a Private SubnetEC2 Instance ConnectEC2 instance must have public IPV4Systems Manager Session Manager ⇒ interact with EC2 through shell in Browserneeds three interface endpoints: com.amazonaws.region.ssm, com.amazonaws.region.ec2messages, com.amazonaws.region.ec2AMI needs to have the SSM AgentSSH - using Bastion Host:Deploy EC2 into private subnet and connect to Application (running on ECS)Create a Bastion host in the public subnet while developing:![[/Untitled 4 9.png|Untitled 4 9.png]]  
    
      
    
    > [!important]  
    > SSH - Useful Commandscreate rsa keyaws ec2-instance-connect send-ssh-public-key --instance-id BASTION_HOST_INSTANCE_ID --instance-os-user ec2-user --ssh-public-key file://KEY_NAME.pubuse EC2 Instance Connect to provide SSH Public Keyssh-keygen -t rsa -f KEY_NAMEssh into bastion hostssh -o "IdentitiesOnly=yes" -i KEY_NAME ec2-user@BASTION_HOST_PUBLIC_IP  
      
    
      
    
- DB Deployment on AWS - RDS> [!important]  
    > Initial ThoughtsCreate RDS Instance Database → essentially an EC2 Instance with EBS for persistent storage which is managed by AWSShut down the EC2 at the end of the day ⇒ take a snapshot and store in an S3Restore the DB from the snapshot at the start of the day
