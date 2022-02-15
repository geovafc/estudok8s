Minikube
	Especificar que vou usar o docker para trabalhar com containers no kubernetes
		minikube config set driver docker
		
	Remove ....
		minikube delete 
		
	Starta inicia o seu Cluster
		minikube start 
		
Kubectl 
	Criação do Pod. Onde o que vier depois de run é o nome do run e a imagem do container que será usada vem após --image
		kubectl run nginx --image nginx
	
	Criação de um Pod usando uma definição de um arquivo local. Para esse comando funcionar, preciso está no mesmo nível de 		  localização do arquivo. pod.yaml é o arquivo com as informações do meu POD. 
		kubectl create -f pod.yaml ou kubectl apply -f pod.yaml 
	
	Excluindo um Pod, nginx-4 é o nome do Pod que eu quero deletar.  
		kubectl delete pod nginx-4
	
	Verifica as informações dos Pods criados
		kubectl get pods
			NAME (nome do pod)    READY (pod/containers)   STATUS (se o container está sendo criado ou executando)    				RESTARTS (quantas vezes o container já foi restartado)   AGE (tempo de vida do Pod)
			
		kubectl get pods -o wide
			Mostra as mesmas informações acima seguido de: IP (id do pod)           NODE (nome do worker node)       
			NOMINATED NODE   READINESS GATES
	
	Verificar informações sobre a estrutura das configurações do Pod
		kubectl describe pod nginx (nome do pod)
		
				Endereço do node (está no node minikube no endereço de ip 192.168.49.2 )
				Node:         minikube/192.168.49.2
				....
				
				Número ip do Pod
				IP:           172.17.0.3
				Pode ter uma lista de ip
				IPs:
  					IP:  172.17.0.3
  				Containers no Pod
  				Containers:
				  nginx:
				    Container ID: ...
				    ...
				Condições do Pod
				Conditions:
  				    Type              Status
  				...
  				
  				Eventos que ocorreram no Pod
				Events:
				  Type    Reason     Age   From               Message
				  ----    ------     ----  ----               -------
				  Normal  Scheduled  24m   default-scheduler  Successfully assigned default/nginx to minikube

	
	Verificar replicasets e replicationcontroller no meu node
        kubectl get replicaset 

        kubectl get replicationcontroller    	


    Deletar replica set e replicationcontroller pelo nome do arquivo
        kubectl delete replicaset nomearquivo-rs 

        kubectl delete replicationcontroller nomearquivo-rs 

    Criação de replicas dos pods
        kubectl create -f nomedoarquivo.yaml

    Verificar os replicasets no meu node
        kubectl get replicaset
        
        NAME          DESIRED   CURRENT   READY   AGE
        frontend-rs   2         2         2       87s

        NAME = nome do replicaset
        DESIRED = quantidade de replicas dos pods desejadas
        CURRENT = quantidade de replicas dos pods em produção
        READY = quantidade de replicas dos pods prontas pra uso
        AGE = tempo de criação dos pods

	Comando para verificar os detalhes do meu replicaset e também dos eventos que ocorreram nele

		kubectl describe replicaset frontend-rs

	Escalar os pods. Com isso o tráfego será direcionado para o 4 pods e a carga será 
	balanceada, Escala Up. Se quiser fazer um escala Down é só diminuir o número de replicas. 
		kubectl scale replicaset frontend-rs --replicas=4

	Criação do deployments. O K8s vai identificar o tipo de objeto do arquivo 
	e irá criar um deployment. O que vem depois de -f é o caminho para o arquivo de 
	deployment.

		kubectl create -f deployments/deployment.yaml 

	Obter informações do deployments	

		kubectl get deployment
		NAME          READY   UP-TO-DATE   AVAILABLE   AGE
		frontend-dp   3/3     3            3           2m29s

		NAME = nome do deployment
		READY = pods criados
		UP-TO-DATE= atualizados
		AVAILABLE = pod disponíveis


	Obter tudo que estiver no meu cluster: pods, replicasets, deployment
		
		kubectl get all

	Obter detalhes do deployment e os eventos. O que vem depois de deployment é o nome 
	do objeto do deployment

		kubectl describe deployment frontend-dp

	Verificar status do deployment. Deployments depois de status não é o diretório
	do arquivo de deployment.

		kubectl rollout status deployments/frontend-dp

	Deletar o deployment

		kubectl delete deployment frontend-dp

	Hisórico dos deployments pra saber as revisões criadas. 
		kubectl rollout history deployment/frontend-dp

		REVISION  CHANGE-CAUSE
		1         <none>

		REVISION= número da revisão do deployment
		CHANGE-CAUSE= Causa da mudança. Pode ser informado no momento em que for fazer o deployment. Armazena o comando que foi usado pra fazer o deployment. 

	Aplicar atualizações feitas nos arquivos de definição. Quando esse comando for feito
	para um arquivo de deploy, ele irá realizar a atualização do deploy dos pod. 
	OBS: se um pode tiver sendo acessado pelos clientes da aplicação, ele não será 
	atualizado até que fique ocioso, isso na estratégia rolleupdate.

		kubectl apply -f deployments/frontend.yaml
