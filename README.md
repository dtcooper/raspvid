# Raspberry Pi Video Player

Repository for my home project, a video player on an old CRT TV using the Raspberry
PI. I use a 3 B+, but it should work on any newer board.

## Steps to install

### On the Raspberry Pi 3 or 4

1. Install [Raspberry Pi OS Lite (64-bit version)](https://www.raspberrypi.com/software/operating-systems/).
2. Install Make, Docker, and Docker Compose
    ```bash
    # Install Docker + Compose
    curl -fsSL https://get.docker.com | sh
    ```
3. Clone repo, build containers
    ```bash
    git clone https://github.com/dtcooper/raspvid.git
    cd raspvid
    make build
    ```
4. Copy over and edit the `.env` file
    ```bash
    cp .env.sample .env
    ```
5. Build and run,
    ```bash
    docker compose build
    docker compose up -d
    ```
