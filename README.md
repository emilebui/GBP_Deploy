# Genshin Ban Pick

## Introduction

- Genshin Ban Pick (GBP) is a 2-player web game that you can *play* with your friends.
- GBP let you draft your Genshin team by interacting in a ban pick match with your friend
- After both player has established their team in GBP, they can use said teams to play in Genshin Impact
- This web app is inspired by Genshin Impact Championship in China:
  - More info: https://www.youtube.com/watch?v=ddSGuDO7n5U

## Motivation

- There are few things that I want to achieve by working on this project:
  - Design a highly-scalable web-socket related application
  - Create a finished product (from design to production)
  - Trying out front-end coding
- More Info about development, read here: [dev_blog](https://medium.com/@dragonblade9x/making-a-multiplayer-web-game-with-websocket-that-can-be-scalable-to-millions-of-users-923cc8bd4d3b)

## Other Features:

- `Chat`: Player can chat with each other through chat box, spectator can also chat as well
- `Spectate`: The match can be watch by other, simply by just click on the spectator link
- `Reconnection`: Player can reconnect to the existing match by simply go to the game link

## Architecture

```
                 ┌─────────────┐
                 │             │
                 │    Users/   │
           ┌─────►   Browsers  ◄──────┐
           │     │             │      │
           │     │             │      │
           │     └─────────────┘      │
           │                          │
           │                          │
    html/js│                          │ websocket
           │                          │
           │                          │
           │                          │
           │                          │
           │                          │
       ┌───▼──────────────────────────▼───┐
       │                                  │
       │              Ingress             │ ...
       │                                  │
       └▲──────────────────────────────▲──┘
        │                              │
        │                              │
        │                              │
        │                              │
        │                              │
┌───────▼─────┐                 ┌──────▼──────┐
│             │                 │             │
│             │                 │             │
│      FE     │ ...             │     WS      │ ...
│             │                 │             │
│             │                 │             │
│             │                 │             │
└─────────────┘                 └───────▲─────┘
                                        │
                                        │
                                        │
                                        │  pubsub
                                        │
                                        │
                                ┌───────▼───────────┐
                                │                   │
                                │                   │
                                │       Redis       │ (Clusterable)
                                │                   │
                                │                   │
                                └───────────────────┘
```

## Components

- **WS Server**: https://github.com/emilebui/GBP_BE_WS
  - A backend websocket server for handling game requests
- **FE Server**: https://github.com/emilebui/GPB_FE_SolidJS
  - A front end server which is responsible for rendering html and js for client
- **Redis**:
  - Pubsub server and game info storage
- **Ingress Nginx**:
  - Reverse proxy + Load balancer

### Note
- Every component in here can be scalable, even the websocket server which can be scale up to millions of instance to be able to serve ~billions users (if there is enough resource)
- More information about the design, read here: 

## Credits

- The front-end of this project was modified from Pustur/genshin-impact-team-randomizer
  - https://github.com/Pustur/genshin-impact-team-randomizer

# Usage

## Requirements
- Install Kubernetes
- Install ingress-nginx extensions

## Deployments
### Do the following steps
- **Setup config_map**: `kubectl apply -f config_map.yaml`
- **Deploy redis**: `kubectl apply -f redis.yaml`
- **Deploy WS**: `kubectl apply -f ws.yaml`
- **Deploy FE**: `kubectl apply -f fe.yaml`
- **Deploy Ingress**: `kubectl apply -f ingress.yaml`

### Note
- After deploying ingress, you should wait a bit for ingress to return an IP address
- Then, you can access the system
- For simple deployment (one-node deployment), you can checkout this deployment project instead (using docker-compose)
  - https://github.com/emilebui/GBP_deploy_dev
