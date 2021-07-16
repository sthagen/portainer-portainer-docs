---
hide: toc
---

# :fontawesome-brands-docker: Deploying Portainer Business in Docker Swarm
Portainer is comprised of two elements, the Portainer Server, and the Portainer Agent. Both elements run as lightweight Docker containers within a Swarm cluster. Due to the nature of Docker, there are many possible deployment scenarios, however, we have detailed the most common below. Please use the scenario that matches your configuration.

<!-- **Note:** The recommended deployment mode when using Swarm is using the Portainer Agent. -->

Regardless of your configuration, you will need:

* The latest version of Docker installed and working, with swarm mode enabled and your nodes added. You can refer to [the Docker documentation](https://docs.docker.com/get-docker/) for platform-specific instructions.
* Administrator or sudo access on the machine that will host the Portainer Server service.
* Administrator or sudo access on the machines where you will deploy the Portainer Agent if you need to add more endpoints to your Portainer instance.

By default, Portainer will expose the UI over port `#!Ruby 9000` and expose a TCP tunnel server over port `#!Ruby 8000`. The latter is optional and is only required if you plan to use the Edge compute features with Edge agents.

To learn more about the requirements please visit the [requirements](/v2.0-be/deploy/requirements/) page.

!!! Warning "Agent Versions"
    Always match the agent version to Portainer Server version. i.e., while installing or upgrading to Portainer 2.6 make sure all the agents are also version 2.6. 


## :fontawesome-solid-paper-plane: Portainer Deployment

Use the following Docker commands to deploy the Portainer Server; note the agent is not needed on standalone hosts, however it does provide additional functionality if used:

=== "Docker Swarm on Linux"
    !!! Abstract ""        
        ### Portainer Server Deployment
        Portainer can be directly deployed as a service in your Docker cluster. Note that this method will automatically deploy a single instance of the Portainer Server, and deploy the Portainer Agent as a global service on every node in your cluster.

        ```shell
        curl -L https://downloads.portainer.io/portainer-ee-agent-stack.yml -o portainer-agent-stack.yml
        ```
        ```shell
        docker stack deploy -c portainer-agent-stack.yml portainer
        ```

        <b>Note</b>: By default this stack doesn't enable Host Management Features, you need to enable from the UI of Portainer.
        
        ### Portainer Agent Only Deployment
        Deploy Portainer Agent on a remote LINUX Swarm Cluster as a Swarm Service, run this command on a manager node in the remote cluster.

        First create the network:

        ```shell
        docker network create --driver overlay --attachable portainer_agent_network
        ```

        The following step to deploy the Agent:

        ```shell
         docker service create --name portainer_agent --network portainer_agent_network --publish mode=host,target=9001,published=9001 -e AGENT_CLUSTER_ADDR=tasks.portainer_agent --mode global --mount type=bind,src=//var/run/docker.sock,dst=/var/run/docker.sock --mount type=bind,src=//var/lib/docker/volumes,dst=/var/lib/docker/volumes --mount type=bind,src=/,dst=/host portainer/agent:2.4.0
        ```


=== "Docker Swarm on Windows WSL / Docker Desktop"
    !!! Abstract ""    
        Before you can deploy Portainer in Docker Swarm running in Windows, you need to install WSL. [Read this guide to know more about WSL/WSL2](https://docs.microsoft.com/en-us/windows/wsl/install-win10){target=_blank}

        Use the following Docker Swarm commands to deploy the Portainer Server; note the agent is not needed on standalone hosts, however it does provide additional functionality if used:
        
        ### Portainer Server Deployment
        Portainer can be directly deployed as a service in your Docker cluster. Note that this method will automatically deploy a single instance 
        of the Portainer Server, and deploys the Portainer Agent as a global service on every node in your cluster.

        Remember to initiate the Docker Swarm mode when you use Docker Desktop. You can do this running the following command:

        ```shell
        docker swarm init
        ```

        The terminal will reply with this:

        ```shell
        Swarm initialized: current node (15gbf4d66mvzk3die00sgirpf) is now a manager.

        To add a worker to this swarm, run the <code>swarm join</code> command that is provided when initializing the swarm (it will look similar to the following):

            docker swarm join --token SWMTKN-1-096qbnf2b9yywagu5ht3731zlpkeqazgctffolntsiljfp0m34-c4snnxplgwq2bd1ohta8k48b9 192.168.65.3:2377

        To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
        ```

        Once this is done, you can continue with the installation by running the following command:

        ```shell
         curl -L https://downloads.portainer.io/portainer-ee-agent-stack.yml -o portainer-agent-stack.yml
        ```
        ```shell
        docker stack deploy -c portainer-agent-stack.yml portainer
        ```

        ### Portainer Agent Only Deployment
        First create the network:

        ```shell
        docker network create --driver overlay --attachable portainer_agent_network
        ```
        
        To deploy Portainer Agent on a Swarm Cluster as a Swarm Service, run this command in a manager node in the cluster:

        ```shell
         docker service create --name portainer_agent --network portainer_agent_network --publish mode=host,target=9001,published=9001 -e AGENT_CLUSTER_ADDR=tasks.portainer_agent --mode global --mount type=bind
        src=//var/run/docker.sock,dst=/var/run/docker.sock --mount type=bind,src=//var/lib/docker/volumes,dst=/var/lib/docker/volumes –-mount type=bind,src=/,dst=/host portainer/agent:2.4.0
        ```

=== "Docker Swarm on Windows Container Service"
    !!! Abstract ""
        To run Portainer in a Windows Server/Desktop Environment, you need to create exceptions in the firewall. These can be easily added through PowerShell by running the following commands:

        ```shell
        netsh advfirewall firewall add rule name="cluster_management" dir=in action=allow protocol=TCP localport=2377
        ```

        ```shell
        netsh advfirewall firewall add rule name="node_communication_tcp" dir=in action=allow protocol=TCP localport=7946
        ```

        ```shell
        netsh advfirewall firewall add rule name="node_communication_udp" dir=in action=allow protocol=UDP localport=7946
        ```

        ```shell
        netsh advfirewall firewall add rule name="overlay_network" dir=in action=allow protocol=UDP localport=4789
        ```

        ```shell
        netsh advfirewall firewall add rule name="swarm_dns_tcp" dir=in action=allow protocol=TCP localport=53
        ```

        ```shell
        netsh advfirewall firewall add rule name="swarm_dns_udp" dir=in action=allow protocol=UDP localport=53
        ```

        You also need to install Windows Container Host Service and install Docker.

        ```shell
        Enable-WindowsOptionalFeature -Online -FeatureName containers -All
        ```
        ```shell
        Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
        ```
        ```shell
        Install-Package -Name docker -ProviderName DockerMsftProvider
        ```

        Lastly, you need to restart your Windows Server. After it has restarted, you're ready to deploy Portainer.

        ### Portainer Server Deployment

        You can use our YML manifest to run Portainer in Windows using Windows Containers. In Powershell, run:

        ```PowerShell
         curl https://downloads.portainer.io/portainer_ee_windows_stack.yml -o portainer_windows_stack.yml
        ```
        ```PowerShell
        docker stack deploy --compose-file=portainer_windows_stack.yml portainer
        ```
        Now, you can navigate to http://localhost:9000 or the IP of the server and start using Portainer.

        
        ### Portainer Agent Only Deployment

        To run Portainer Agent in a Windows Container scenario, you need to execute the following commands:

        ```PowerShell
        curl -L https://downloads.portainer.io/agent-ee-stack-windows.yml -o agent-stack-windows.yml && docker stack deploy --compose-file=agent-stack-windows.yml portainer-agent
        ```


<br>
## :material-note-text: Notes

[Contribute to these docs](https://github.com/portainer/portainer-docs/blob/master/contributing.md){target=_blank}