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

Once you are connected to the remote server, you can run a Jupyter Notebook server as usual using the command:

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





