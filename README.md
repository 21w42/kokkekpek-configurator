# Configurator
## Requirements
* Ubuntu 18.04 or later
* Docker
* Docker Compose

## Use
### 1. Place config.json in root directory.
Examaple
```json
{
  "directories": {
    "traefik.acme": {
      "path": "./data/traefik/acme",
      "permissions": 775
    },
    "traefik.log": {
      "path": "./log",
      "permissions": 775
    }
  },
  "values": {
    "email": "my.email@gmail.com"
  },
  "show": {
    "tls": true
  }
}
```

### 2. Run
```
wget -q https://raw.githubusercontent.com/kokkekpek/configurator/v1.0.0/lib/index.js index.js | docker run -t --rm --name script -v "$PWD":/usr/src/app -w /usr/src/app node:14.5-alpine node . && rm index.js
```

## What configurator do
### 1. Create directories.
Config example
```json
{
  "directories": {
    "traefik.acme": {
      "path": "./data/traefik/acme",
      "permissions": 775
    },
    "traefik.log": {
      "path": "./log",
      "permissions": 775
    }
  }
}
```
Create directories and set permission
* ./data/traefik/acme
* ./log

### 2. Search \*.template.\* files.
Examples:
* one.template.json
* data/sizes.template.yml 

### 3. Copy template files.
Examples:
* one.template.json > one.json
* data/sizes.template.yml  > data/sizes.yml

### 4. Search placeholders.
Two types of placeholder
* Simple
* Comment \# or //

#### Simple placeholder examples
* {cfg.name}
* {cfg.user.email}

### Comment placeholder examples
* \#{cfg.name}
* //{cfg.name}

All placeholder contains **cfg.**

### 5. Replace placeholders.
Example of config
```yaml
paths:
  log: {cfg.traefik.log}
name: Monitoring
email: {cfg.email}
#{cfg.tls}tls: true
#{cfg.tls}tls: true
#{cfg.admin}admin:
#{cfg.admin}  name: root
#{cfg.admin}  password: 12345
```

config.json
```json
{
  "directories": {
    "traefik.log": {
      "path": "./log",
      "permissions": 775
    }
  },
  "values": {
    "email": "my.email@gmail.com"
  },
  "show": {
    "tls": true,
    "admin": false
  }
}
```

Result
```yaml
paths:
  log: ./log
name: Monitoring
email: my.email@gmail.com
tls: true
#admin:
#  name: root
#  password: 12345
```

Configurator get data for Simple placeholders from "values" object and "directories" paths.
For comment placeholders configurator check data in "show" object and replace to empty string if value is true.