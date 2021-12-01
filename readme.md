## TICK STACK
  Run the complete tick stack using docker compose method. This uses the latest versions of each individual docker image except influxdb.
* telegraf:latest
* influxdb:1.8
* chronograf:latest
* kapacitor:latest

## Steps to create docker images of each component and using it

1. Clone Repo and change the directory to where docker-compose.yml and telegraf.conf files are located

2. Run docker-compose.yml file

```
docker-compose up -d    

```

3. Check the status of each container

```
docker ps -a

```

4. If all container are up, Run below command

```
docker run lucj/genx:0.1 -type cos -duration 30d -min -100  -max 100 -step 1h > /tmp/data

```

The above command will use the lucj/genx Docker image to generate data following a cosine distribution. Data are generated for 30 days, with a one day period, min/max values of -100/100 and a sampling step of one hour, for testing purpose.


5. Run below command, which send the data to the Telegraf HTTP endpoint:

```
PORT=8094                                                                                
endpoint="http://localhost:$PORT/write"
cat /tmp/data | while read line; do
  ts="$(echo $line | cut -d' ' -f1)000000000"
  value=$(echo $line | cut -d' ' -f2)
  curl -i -XPOST $endpoint --data-binary "temp value=${value} ${ts}"
done

```

6. Now open the browser and open <a href= 'http://localhost:8888/'>http://localhost:8888/</a>

7. Go to the Explore tab and type below query and click on Submit Query Button.

```
select "value" from "test"."autogen"."temp"

```

8. Finally, we will be able to see a Cosine distribution plot.
