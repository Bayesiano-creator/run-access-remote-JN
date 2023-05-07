# Run and acess a Jupyter Notebook hosted on a remote server

This tutorial will show you how to run a Jupyter Notebook on a remote server and connect to it from another computer as if it were running locally. The goal is to make it a comfortable and fast process, especially for cases where you need to connect frequently and sometimes to more than one Jupyter Notebook server. To achieve this, we'll be using two tools: the **screen** command and the **Termius** SSH client app.

## Prerequisites

* Jupyter Notebook (https://jupyter.org/install)
* Screen (https://linuxize.com/post/how-to-use-linux-screen/)
* Termius (https://termius.com/free-ssh-client-for-linux)

## Introduction

The simplest and most common way to perform this task is by using SSH tunnels. The first thing you need to do is open a terminal and connect to the remote server. To do this, you will need:

 * IP address ```<IP>```,
 * port ```<PORT>```,
 * user ```<USER>``` and
 * password ```<PWD>```

Open a terminal on your local machine and execute the command to **start a session on the remote server**:

```
[local_user@local_host]$ ssh <USER>@<IP> -p <PORT>
```

Once the command is executed, it will ask to enter the password:

```
<USER>@<IP>'s password: <PWD>
```

Once you are connected to the remote server, you can **run a Jupyter Notebook server** as usual using the command:

```
[remote_user@remote_host]$ jupyter notebook
```

By default, Jupyter Notebook opens a window in the browser with the address of the local server where it is running. The address is in the format localhost:```<REMOTE-JN-PORT>``` and the port number ```<REMOTE-JN-PORT>``` is automatically assigned.

In this case, we don't want to open a window in the remote browser because it will be done on the local machine, and it is recommended to assign the port number manually because it will be useful later on. To do this, use the following command:

```
[remote_user@remote_host]$ jupyter notebook --no-browser -port <REMOTE-JN-PORT>
```

To access the remote Jupyter Notebook server from the local machine, you need to **send the remote port to a local port**. This is called port forwarding and can also be done with the ssh command. Select a local port ```<LOCAL-JN-PORT>```, open another terminal on the local machine, and execute the command:

```
[local_user@local_host]$ ssh -L <LOCAL-JN-PORT>:localhost:<REMOTE-JN-PORT> <USER>@<IP> -p <PORT>
```

You will need to enter the password again.

This completes the procedure. If you enter the browser and open the address localhost:```<LOCAL-JN-PORT>```, you can use the Jupyter Notebook running on the remote server.

This procedure has two major disadvantages:

1.   The remote server session where the Jupyter Notebook was executed must be active all the time. If it expires or the connection is lost, the Jupyter Notebook server will be completely closed and any active processes will be killed.
2.   The entire procedure has to be done from scratch every time you want to connect.

The **screen** command solves the first disadvantage, and the **Termius** app proposes an alternative for the second.

## Screen command

Screen is a tool that allows you to manage multiple virtual terminals within a single terminal window. You can create as many virtual terminals or windows as you need using Screen. Any processes running in a Screen session will keep running even if you switch to a different window or get disconnected, ensuring that you don't lose any progress or data. Here you can find a basic tutorial: https://linuxize.com/post/how-to-use-linux-screen/.

The Jupyter Notebook servers can be executed in Screen sessions. If your SSH session to the remote server is terminated, **the Jupyter Notebook will still be running**. To achieve this, start a session on the remote server using ssh:

```
[local_machine]$ ssh <USER>@<IP> -p <PORT>
```

and enter your password. Create a Screen session (use a manual session name ```<SESSION-NAME>```) with the command:

```
[remote_server]$ screen -dmS <SESSION-NAME>
```

The option -dmS allows you to create a new session named ```<SESSION-NAME>``` but stay in your local window. You can now use ```screen -ls``` to list all screen sessions and use ```screen -r <SCREEN-NAME>``` to acess (reattach) the screen session. It has to look like this:

```
[remote_server]$ screen -dmS <SESSION-NAME>
[remote_server]$ screen -ls
There are screens on:
        <SCREEN-ID>.<SCREEN-NAME> (Detached)
[remote_server]$ screen -r <SCREEN-NAME>
```

Once you've executed the above three commands you'll be inside the```<SESSION-NAME>``` session and you'll see a new window, without the above commands. You can verify that you're inside that session by running again ```screen -ls```. Now the screen will be Attached instead of Detached. Next, run a Jupyter Notebook server:

```
[remote_server]$ screen -ls
There are screens on:
        <SCREEN-ID>.<SCREEN-NAME> (Attached)
[remote_server]$ jupyter notebook --no-browser --port <REMOTE-JN-PORT>
[I 08:58:24.417 NotebookApp] Serving notebooks from local directory: /Users/user
[I 08:58:24.417 NotebookApp] 0 active kernels
[I 08:58:24.417 NotebookApp] The Jupyter Notebook is running at: http://localhost:<REMOTE-JN-PORT>/
[I 08:58:24.417 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```


then press ```Ctrl+a``` ```d``` to detach the current session and you'll return to the initial session. It should look like this:

```
[remote_server]$ screen -dmS <SESSION-NAME>
[remote_server]$ screen -ls
There are screens on:
        <SCREEN-ID>.<SCREEN-NAME> (Detached)
[remote_server]$ screen -r <SCREEN-NAME>
[remote_server]$ 
```
Now you can disconnect your initial session and the Jupyter Notebook server will still be running. 

If you want to close the screen session, reattach again the session with ```screen -r <SCREEN-NAME>```, stop the Jupyter Notebook server and type ```exit```. This wil take you again to the initial session. 

## Termius app

Termius is a cross-platform SSH client application that allows you to securely connect to and manage remote servers or computers. It provides a graphical user interface for managing SSH connections, allowing you to easily create and save connections. 


Download and create an account to continue this tutorial. This link has a quick start for Terminus app: https://support.termius.com/hc/en-us/articles/4578043155737-Quick-Start.

### *Create a new host*

Once Termius has been installed and you're logged, start by creating a new host:

![fillHost](https://user-images.githubusercontent.com/77857908/236705527-985fbae5-4545-429d-9b0e-faba4b3dff31.png)

### *Select an existing host*

Double click on an existing host. If you saved the user and password when creating the host, it'll connect you to the remote server automatically:

![opening](https://user-images.githubusercontent.com/77857908/236705582-59a5366c-7efe-40e8-9a08-13f3508ceb41.png) ![in](https://user-images.githubusercontent.com/77857908/236705795-47f5fbf9-11bc-48ce-a6b1-1e812c908804.png)

Now you can start a session on the remote server with just a double click.

### *Port forwarding* 

To this part, we assume you have done the previous section (Screen command), so now we are going to create and save a **Port Forwarding** in order to open locally a Jupyter Notebook server that is running on a remote server.

Go to the Port Forwarding sheet and select new forwarding. The procedure consist of three steps: select the local port and binding address, select the intermediate host and select the destination host.

In the first step you type ```<LOCAL-JN-PORT>``` on local port number and localhost on Bind address:

![local](https://user-images.githubusercontent.com/77857908/236706271-194195e1-2040-4e9b-9d17-4a06b6cb1486.png)

Second step consists in selecting an existing host. Select the host where the Jupyter Notebook server is running. For the final step, type localhost on Destination address and ```<REMOTE-JN-PORT>``` on Destination port number:

![remote](https://user-images.githubusercontent.com/77857908/236706368-50b3a7a3-771b-44a4-ae21-8415e363761f.png)

Once the port forwarding has been created and saved, you can double click on it to turn it On/Off. If it's on, visit localhost:```<LOCAL-JN-PORT>``` on your local machine and you'll be able to use the Jupyter Notebook server, as long as it still be running on the remote server.  
