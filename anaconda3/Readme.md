# Ananconda 3
miniconda 3

## How to run
- goto directory
- `docker build -t img-anaconda3 .`
- Run container
```sh 
docker run -d \
  --name my_anaconda3 \
  --network backend \
  -v /Users/harish/WorkSpace/python:/opt/notebooks \
  -p 8888:8888 \
  img-anaconda3
```
