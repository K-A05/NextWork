<p align="center">
  <img src="./assets/logo.jpg" alt="NextWork projects logo" width="160" />
</p>

# NextWork Projects

A collection of hands-on cloud, distributed systems and security projects, built by hand (not copy-pasted) to understand what each step is doing. Each project follows a [NextWork](https://nextwork.ai) guide and has its own README with notes, screenshots and lessons learned.

| Project | Focus | Stack | Details |
|---|---|---|---|
| [AWS VPC](./AWS_VPC) | Cloud networking | AWS VPC, Subnets, Internet Gateway, CloudShell/CLI | [README](./AWS_VPC/README.md) |
| [Breaking a Distributed System](./Breaking_distributed_sys) | CAP theorem, replication | Python, Flask, Docker, Docker Compose | [README](./Breaking_distributed_sys/README.md) |
| [AI Security Scanner](./Ai_Scanner_python) | Automated code review | Python, Gemini API, uv, colorama | [README](./Ai_Scanner_python/README.md) |

---

## 1. Build a Virtual Private Cloud

**What it is:** A VPC with a public subnet connected to the internet through an internet gateway, built first in the AWS console and then repeated with the AWS CLI in CloudShell.

**What I learned:** CIDR notation, how subnets must sit inside the VPC's range without overlapping, what makes a subnet "public" (a route to an internet gateway plus auto-assigned public IPv4), and where the CLI and console views can fall out of sync.

```mermaid
flowchart TB
    Internet(("Internet"))
    IGW["Internet Gateway"]

    subgraph AWS["AWS Region"]
        subgraph VPC["VPC - custom IPv4 CIDR block"]
            RT["Route Table<br/>0.0.0.0/0 to IGW"]
            subgraph AZ["Availability Zone"]
                subgraph Subnet["Public Subnet<br/>auto-assign public IPv4"]
                    EC2["EC2 instances<br/>get public IPs on launch"]
                end
            end
        end
        CLI["CloudShell and AWS CLI<br/>create-vpc, create-subnet,<br/>attach-internet-gateway"]
    end

    Internet <--> IGW
    IGW <--> RT
    RT <--> Subnet
    CLI -. "builds the same resources" .-> VPC
```

---

## 2. Break a Distributed System on Purpose

**What it is:** A three-node key-value store written in Flask and run with Docker Compose. I deliberately partition the cluster to watch the CAP trade-off happen, then add last-write-wins conflict resolution so diverged nodes can merge after the partition heals.

**What I learned:** CP vs AP behaviour in practice, why a CP cluster rejects writes during a partition, how AP mode leads to split-brain, and the trade-off of last-write-wins (valid but older data gets discarded).

### Cluster topology

```mermaid
flowchart LR
    Client(["Client"])

    subgraph Compose["Docker Compose network"]
        N1["node1<br/>host :5001 to container :5000"]
        N2["node2<br/>host :5002 to container :5000"]
        N3["node3<br/>host :5003 to container :5000"]
    end

    Client --> N1
    Client --> N2
    Client --> N3
    N1 <-- "/replicate" --> N2
    N2 <-- "/replicate" --> N3
    N1 <-- "/replicate" --> N3
```

### CP vs AP write behaviour during a partition

```mermaid
sequenceDiagram
    participant C as Client
    participant N1 as Node 1
    participant N2 as Node 2
    participant N3 as Node 3 (down)

    Note over C,N3: CP mode - consistency first
    C->>N1: PUT key=value
    N1->>N2: /replicate
    N2-->>N1: ack
    N1-xN3: /replicate (unreachable)
    N1-->>C: Rejected - write rejected to maintain consistency

    Note over C,N3: AP mode - availability first
    C->>N1: PUT key=value
    N1->>N1: write locally first
    N1-->>C: Accepted
    N1->>N2: /replicate (async)
    N1-xN3: /replicate (retry later)
    Note over N1,N3: Nodes now hold different data (split-brain)<br/>Last-write-wins merges them once the partition heals
```

---

## 3. AI Security Scanner for Python

**What it is:** A Python script that sends source files to the Gemini API with a security-focused prompt, then prints a vulnerability report with colour-coded severity ratings. It was tested against a deliberately vulnerable file (`vulnerable.py`).

**What I learned:** Prompt engineering for structured output, working with an LLM API, keeping API keys out of source control with `.env` files, and parsing model output to colourise severity levels with colorama.

**Vulnerabilities found in the test file:** SQL injection, command injection, weak password hashing, and hard-coded credentials.

```mermaid
flowchart LR
    ENV[".env<br/>API key, never committed"]
    Target["Target Python file<br/>e.g. vulnerable.py"]

    subgraph Scanner["main.py"]
        Load["Load key<br/>with dotenv"]
        Prompt["Build security prompt<br/>plus file contents"]
        Parse["Parse severity ratings"]
        Color["Colourise output<br/>with colorama"]
    end

    Gemini["Gemini API"]
    Report["Colour-coded<br/>vulnerability report"]

    ENV --> Load
    Target --> Prompt
    Load --> Prompt
    Prompt --> Gemini
    Gemini --> Parse
    Parse --> Color
    Color --> Report
```

---

## Repo hygiene

Secrets are never committed. `.env`, credential exports and virtual environments are listed in `.gitignore`. To run the scanner yourself, create your own `.env` with a Gemini API key.

## Running the projects

```bash
# Distributed system
cd Breaking_distributed_sys
docker compose up --build

# AI scanner
cd Ai_Scanner_python
uv sync
uv run main.py
```

The AWS VPC project is built in the AWS console and CloudShell, so there is nothing to run locally.

## Author

Karan - built while working through [NextWork](https://nextwork.ai) projects.
