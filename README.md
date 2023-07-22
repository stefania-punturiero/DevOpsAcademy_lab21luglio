# DevOpsAcademy_lab21luglio
Creare una VPC e tutto il necessario perché si abbia un autoscaling group funzionante che operi su istanze EC2.
Requisiti
• Un utente IAM con privilegi sufficienti per gestire le istanze EC2, RDS, VPC, ALB e che 
possa lanciare Cloudformation.
• AWS CLI installata e configurata: 
https://docs.aws.amazon.com/cli/latest/userguide/getting-startedquickstart.html#getting-started-quickstart-new-command
• Usare i tags per taggare le proprie risorse (vedi esempio sezione 2), usare i seguenti
o Key = Nome, Value = “tuo name”
o Key = Cognome, Value “tuo cognome”
o Key = Environment, Value = DevOpsAcademy
• Utilizzare GitHub per la consegna del Template Cloudformation
• L’infrastruttura come codice deve avere due files, uno che descrive le risorse e l’altra 
che contiene il file dei parameters
• L’output del template CF dovrà fornire l’endpoint del bilanciatore che andrà condiviso 
a certifica del completamento dell’esercizio
1. Creazione di una VPC
Una VPC (Virtual Private Cloud) è un segmento della rete cloud privata in un ambiente AWS, 
che fornisce isolamento dal resto delle risorse cloud. Puoi creare subnet all'interno di una 
VPC, configurare gruppi di sicurezza e liste di controllo degli accessi di rete (ACL), tra le altre 
cose.
Il codice CloudFormation per la creazione di una VPC è piuttosto semplice. Hai bisogno di 
specificare il blocco CIDR, che è l'intervallo di indirizzi IP per la VPC, e altre opzioni come 
l'abilitazione del supporto DNS. La risorsa è AWS::EC2::VPC
Per maggiori dettagli sulla creazione di una VPC con CloudFormation, consulta la 
documentazione AWS: 
• https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/awsresource-ec2-vpc.html
2. Creazione di un gruppo di Autoscaling di istanze EC2
Per creare un gruppo di autoscaling, devi prima creare una configurazione di lancio (Launch 
Configuration). Questa definisce il tipo di istanze che il gruppo di autoscaling deve creare. 
Le risorse per creare gruppi di autoscaling sono AWS::AutoScaling::AutoScalingGroup e 
AWS::AutoScaling::LaunchConfiguration Per maggiori dettagli sulla creazione di gruppi di 
autoscaling con CloudFormation, consulta la documentazione AWS:
• https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/awsproperties-as-group.html
• https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/awsproperties-as-launchconfig.html
Nel tuo caso, dovresti specificare un'AMI (Amazon Machine Image) con un sistema 
operativo che supporta Yum (come Amazon Linux), in modo da poter installare Apache e 
PHP. Inoltre, dovresti utilizzare l'opzione UserData per eseguire uno script bash all'avvio di 
ogni istanza, che installerà Apache e PHP e creerà una pagina "Hello World".
3. Creazione di un ALB
Elastic Load Balancing (ELB) è un servizio di AWS che distribuisce automaticamente il traffico 
in ingresso tra molteplici destinazioni, come le istanze Amazon EC2, i container e le funzioni 
IP di rete. Ci sono tre tipi di load balancer che ELB offre: Application Load Balancers, 
Network Load Balancers e Classic Load Balancers. In questo workshop, stiamo utilizzando un 
Application Load Balancer (ALB), che distribuisce il traffico HTTP/HTTPS a più destinazioni. 
Puoi configurare un ALB per inoltrare il traffico in ingresso a più gruppi di destinazioni, 
ognuno dei quali corrisponde a un servizio di back-end, come un gruppo di autoscaling EC2.
In un template CloudFormation, utilizzi il tipo di risorsa
AWS::ElasticLoadBalancingV2::LoadBalancer per creare un ALB. Definisci le proprietà come 
le subnets in cui il load balancer deve essere distribuito, e i gruppi di sicurezza da applicare 
al load balancer. Per agganciare il tuo Application Load Balancer al tuo gruppo di 
autoscaling, crei una risorsa target group AWS::ElasticLoadBalancingV2::TargetGroup e lo 
configuri per inoltrare il traffico alle istanze nel tuo gruppo di autoscaling. Dovrai anche 
creare un listener AWS::ElasticLoadBalancingV2::Listener sul tuo load balancer per inoltrare 
il traffico in ingresso al tuo target group.
