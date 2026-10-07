FROM ubuntu:24.04
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    bash git curl wget \
    python3 python3-pip python3-venv \
    build-essential gcc g++ make \
    gdb file binutils \
    strace ltrace socat netcat-openbsd \
    nmap dnsutils jq \
    unzip vim ca-certificates \
    sqlmap nikto gobuster dirb whatweb \
    binwalk steghide exiftool \
    && rm -rf /var/lib/apt/lists/*

RUN python3 -m pip install --break-system-packages \
    pwntools requests beautifulsoup4 lxml \
    z3-solver capstone unicorn ropper \
    pycryptodome pyjwt

WORKDIR /work
CMD ["/bin/bash"]
