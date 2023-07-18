Secure Shell is an utility which helps us to access other instances
-  For SSH access, port 22 should be open on the target machine.
Can be used on
- Mac
- Linux
- Windows 10 and upper
- If you are on Windows 8 or lower, please use `putty` ![Screenshot 2023-06-05 at 10.55.25 PM](../images%201/Screenshot%202023-06-05%20at%2010.55.25%20PM.png)


### How to use SSH
- Download the instance security keys, can checkout [](EC2.md#^0c4488%7Chere) for how to get one for EC2 instance.
- Check instance's security group, it should have security group which have port 22 open to everyone / `0.0.0.0/0` or open to your IP from which you are access the instance. If not, check out here for how to add one.
- Now copy EC2 instance IP address and run the following command
```sh
ssh ec2-user@3.250.26.200
```
- Here `ec2-user` is already created by default in AMI.
- You'll get error, because we haven't define any key to use when doing SSH
- You can reference key by following
```sh
ssh -i path-to-key.pem ec2-user@3.250.26.200
```
- If you are getting `unprotected private key file`, run the following command on the key file.
```sh
chmod 0400 path-to-key.pem
```
- Once logged in to the instance, you can try commands you like in AMI, something like `ping google.com`
- You can closed the terminal by `ctrl/cmd + z` or by writing `exit`