# run-all-docker-compose

This repository includes script that allows you to run commands like `docker compose up`, `docker compose down` and `docker compose build` in subdirectories of current directory like following:  
```
.
├── all
├── project-a
│   ├── docker-compose.yaml
│   ├── other-directories
│   └── other-files
├── project-b
│   ├── docker-compose.yaml
│   ├── other-directories
│   └── other-files
└── project-c
    ├── docker-compose.yaml
    ├── other-directories
    └── other-files
```  


run following command in parent-directory of docker-compose having projects  
```
cat <<EOF >all
#!/bin/bash

POSITIONAL_ARGS=()

while [[ $# -gt 0 ]]; do
  case $1 in
    -u|--up|up)
      find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c "cd '{}' && if [[ -f docker-compose.yaml ]]; then docker compose up -d; fi &" \;
    shift
    ;;
    -d|--down|down)
      find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c "cd '{}' && if [[ -f docker-compose.yaml ]]; then docker compose down; fi &" \;
    shift
    ;;
    -b|--build|build)
      find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c "cd '{}' && if [[ -f docker-compose.yaml ]]; then docker compose build; fi &" \;
    shift
    ;;
    -r|--run|run)
      find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c "cd '{}' && if [[ -f run.sh ]]; then bash run.sh; fi" \;
    shift
    ;;
    -*|--*)
      echo "Unknown option $1"
      exit 1
    shift
    ;;
  esac
done
EOF

sudo chmod +x all
```  

you can run following commands:
```
./all [OPTIONS]

    -u --up up          run docker compose up in detached mode
    -d --down down      run docker compose down in detached mode
    -b --build build    run docker compose build
    -r --run run        run all run.sh scripts in subdirectories
``` 
