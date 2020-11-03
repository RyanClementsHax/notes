# Hitting Endpoints

## Load testing using Hey
```shell
./hey -m POST -D ./<data file name> -H "Content-Type: text/xml;charset=UTF-8" -H "SOAPAction: http://tempuri.org/IExcelIO/ProcessRequest" -t 0 -n 50 -c 5 <url>
```

## Using curl
```shell
curl -X POST -d @<data file name> -H "Content-Type: text/xml;charset=UTF-8" -H "SOAPAction: http://tempuri.org/IExcelIO/ProcessRequest" <url>
```