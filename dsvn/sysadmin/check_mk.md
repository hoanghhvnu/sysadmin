# Check mk system

## Check mk server
### How start (already installed)
1. Run apache
3. Run omd
```
omd start
```

## Check mk agent
1. Install **xinetd**
```
sudo yum install xinetd
```
2. Download __\*.rpm__ then install with
```
rpm -i <name>
```
### Start
```service xinetd start```
