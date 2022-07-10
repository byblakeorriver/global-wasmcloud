# global-wasmcloud
Experimenting with wasmCloud and NGS

## NGS Credentials
After setting up NGS, copy the credentials file into a file named `ngs.creds` in the creds directory.
The same `ngs.creds` file needs to be on both machines running the nats server.

## Generate `.env` file: <br/>
***Warning***: This will overwrite the `.env` file. <br/>
***Warning***: Once the `.env` file is created, do not share it because it 
contains sensitive information. 
```bash
echo "JS_DOMAIN=core" > .env
echo "WASMCLOUD_CLUSTER_SEED=$(wash keys gen cluster -o json | jq -r '.seed')" >> .env
```

The `.env` file needs to be on both machines running the wasmcloud host in the same directory as the `docker-compose.yaml`.

## Run on machine 1
To start nats and wasmCloud servers:
```bash
HOST_MACHINE_NAME=machine_1 docker-compose up
```
To deploy the actors and capability providers to the wasmCloud host:
```bash
wash ctl start actor wasmcloud.azurecr.io/dogs-and-cats:0.1.0
wash ctl link put MCUCZ7KMLQBRRWAREIBQKTJ64MMQ5YKEGTCRGPPV47N4R72W2SU3EYMU VAG3QITQQ2ODAOWB5TTQSDJ53XK3SHBEIFNK4AYJ5RKAX2UNSCAPHA5M wasmcloud:httpserver ADDRESS=0.0.0.0:8081
wash ctl link put MCUCZ7KMLQBRRWAREIBQKTJ64MMQ5YKEGTCRGPPV47N4R72W2SU3EYMU VCCVLH4XWGI3SGARFNYKYT2A32SUYA2KVAIV2U2Q34DQA7WWJPFRKIKM wasmcloud:httpclient
wash ctl start provider wasmcloud.azurecr.io/httpserver:0.15.0
wash ctl start provider wasmcloud.azurecr.io/httpclient:0.4.0
```

## Run on machine 2
To start nats and wasmCloud servers:
```bash
HOST_MACHINE_NAME=machine_2 docker-compose up
```

## Run on machine 1 or 2
```bash
# The constraint flag ensures we start on a host with that label
wash ctl start actor wasmcloud.azurecr.io/dogs-and-cats:0.1.0 --constraint machine=machine_2
wash ctl start provider wasmcloud.azurecr.io/httpclient:0.4.0 --constraint machine=machine_2
```
