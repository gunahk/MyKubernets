aws ssh 

 ~/.ssh/config 
```
Host bastion
        HostName {Elastic IP Address of Bastion}
        User ec2-user
        IdentityFile ~/.ssh/{your-ssh-key.pem}
Host tester
        ForwardAgent yes
        HostName {Local IP Address of RedTeam Instance}
        User ec2-user
        IdentityFile ~/.ssh/{your-ssh-key.pem}
        ProxyCommand ssh bastion nc %h %p
        ServerAliveInterval 240

```


aws s3 delete objects, create 2 lifecycles
1. Delete the objects older than
<img width="1053" alt="image" src="https://github.com/gunahk/MyKubernets/assets/37513630/87e84560-bc76-4ccc-8950-72681890ab72">

2. Add a marker to delete mark objects
<img width="1325" alt="image" src="https://github.com/gunahk/MyKubernets/assets/37513630/6165ebc1-7fe0-43af-b541-0fa60125db71">
