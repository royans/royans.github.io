---
layout: post
title:  "Building FauxSSH: An interesting experiment"
date:   2026-01-04 12:14:15 +0000
categories: security ssh honeypot ai experiment
---

# Building FauxSSH: An AI-Assisted Honeypot Experiment

I've always been fascinated by honeypots. Recently, I decided to run an experiment: **Could I build a convincing high-interaction SSH honeypot in just a few days, using Google's Antigravity and Gemini models as my coding assistants?**

The result of this experiment is [FauxSSH](https://github.com/royans/fauxssh), a Python-based honeypot that uses a Generative AI model as its core engine.

This project draws inspiration from my [earlier web-based honeypot experiments](https://royans.net/security/honeypot/genai/2025/12/28/Smarter-honeypots-using-GenAI-v2.html) in late 2024. The core premise is that LLMs have become so sophisticated that [distinguishing generated content from reality is increasingly difficult](https://royans.net/llm/code/execution/cryptographic/2025/12/23/the-cryptographic-wall-fast-external-verification-of-code-execution.html), making them perfect engines for high-interaction deception.

---

## The Core: LLM as the Engine

A key feature of FauxSSH is that it offloads the "brain" of the operating system to a Large Language Model (Google Gemini). This allows for a depth of interaction that is impossible with static scripts.

### Generating "Fake" Content
In a traditional honeypot, if an attacker runs `cat /etc/nginx/nginx.conf`, the system usually returns a "File not found" error unless the developer manually added that specific file.
In FauxSSH, the LLM generates the content on the fly. It understands the persona (e.g., "Debian 11 Web Server") and hallucinates a plausible configuration file.

```bash
ssh blogofy.com "cat /etc/nginx/nginx.conf | head -20"
```

```nginx
user nginx;
worker_processes auto;

error_log /var/log/nginx/error.log notice;
pid       /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" ' ;
                      '$status $body_bytes_sent "$http_referer" ' ;
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
```
- **Adaptive Persona**: When I noticed bots constantly hunting for high-end GPUs, I tweaked the persona prompts. Now, running `nvidia-smi` instantly shows fake H100s, baiting the attackers to drop their mining payloads.
- **Fake Internals**: I guide the LLM to hallucinate a consistent local network (`10.0.0.x`) and even fake the output of `curl` or `wget` requests to make it look like external connections are succeeding, further maintaining the illusion.

### Understanding Intent & Risk
I don't just regex match commands; I ask the LLM to understand them.
Every command entered by an attacker is sent to the model for analysis.
- **Intent**: Is the user trying to check system resources (`free -m`) or download a rootkit?
- **Risk Assessment**: The LLM assigns a "Risk Score" (0-10) to every interaction. This allows me to filter out thousands of automated `uname` checks and focus on the one session where a human tried to edit `/etc/shadow`.

![Risk Analysis table showing risk scores for commands](/assets/imgs/2026-01-04/fauxssh-risk-analysis.png)

### Simulating Malware Execution
This is where it gets interesting. If an attacker uploads a script (say, `exploit.sh`) and runs `bash exploit.sh`, I do **not** execute it. That would be dangerous.
Instead, I feed the script content to the LLM and ask: *"If this script were run on this machine, what would the output be?"*
The model parses the code, understands its logic (e.g., "it tries to download a file from X and compile it"), and generates the expected terminal output. The attacker sees their script "run" and even "succeed," but no code was ever executed on the host.

---

## Fingerprinting the Attacker

While the LLM handles the deception, I attempt to use signals to identify who is on the other end.

- **SSH Client & Agent**: I look at the raw client version string (e.g., `SSH-2.0-Go`, `SSH-2.0-OpenSSH_8.2p1`). This is often the first indicator of a botnet versus a human.
- **HASSH Fingerprinting**: I calculate the **HASSH** of the SSH handshake. This allows me to identify specific botnet implementations even if they spoof their client string.
- **Command Uniqueness**: By tracking the frequency of unique command hashes, I can often spot mass-scanning campaigns (which use identical command chains across thousands of IPs) versus unique, manual keyboard input.

![FauxSSH Dashboard showing attacker sessions and commands](/assets/imgs/2026-01-04/fauxssh-dashboard.png)

---

## A Hybrid Architecture

Purely generative honeypots can be slow and expensive. FauxSSH is a **hybrid system**.
- **Pre-Populated Data**: Common, high-frequency commands (`ls /`, `whoami`, `pwd`) are served from a local cache or a lightweight virtual filesystem.
- **Dynamic Hallucination**: Novel commands, complex file reads, or interaction with "malware" are punted to the LLM.

This balance keeps latency low for the attacker while maintaining the infinite depth of the AI.

---

## The Challenge of Simulating a Shell

One challenge I faced was building a believable shell environment in Python without actually spawning a shell (which is a massive security risk).

### Safety First (No Exec)
I decided to avoid `eval()` or `subprocess.run()`. Every shell feature had to be re-implemented in safe Python logic.

### Complexity of Emulation
Imitating `bash` is surprisingly hard.
- **Chaining**: I support `&&`, `||`, and `;` to allow attackers to chain commands.
- **Piping**: I implemented basic piping (`|`) where the output of one command is fed as text input to the simulated next command.
- **Variables**: I track session state so that `A=1; echo $A` actually works.

Implementing these features (variables, pipes, persistence) typically takes weeks of testing to handle edge cases. However, using **coding assistants** accelerated this massively. I could simply describe the logic *"Implement a state machine that handles variable expansion for nested commands like `cpus=$( (nproc) | head -1 )`"* and the assistant would scaffold the complex parsing logic, allowing me to focus on the security architecture.

---

## Next Steps and Future

I am just getting started. The roadmap for FauxSSH includes:

### LLM Agnosticism
While I built this on Google Gemini, the architecture is designed to be model-agnostic. I plan to add support for other local and cloud models (like Llama 3 or GPT-4o) to make the backend swappable.

### Fake SSH Chaining (Pivoting)
A common attacker tactic is to use a compromised host to "pivot" or SSH into other machines on the internal network. I plan to implement **fake SSH chaining**, where running `ssh 10.0.0.2` inside the honeypot seamlessly drops the attacker into *another* simulated session with a different persona (e.g., a Database Server), deepening the rabbit hole.

### Scalability
Currently, I use SQLite for simplicity. As I deploy more sensors, I plan to migrate to a more scalable backend like **MariaDB** to handle millions of events across distributed honeypot fleets.

## Conclusion

FauxSSH is an experiment in what's possible when you combine traditional security concepts with modern Generative AI. It’s not just a tool for catching hackers; it’s a playground for observing how AI can simulate reality.

By no means is this the only honeypot out there, and I am certainly still learning. Given the experimental nature of this project, there will be bugs, but this was intended as a learning experience for me. I hope that sharing this work is helpful for others who decide to use it or learn from it.

The project is open source and available here: [github.com/royans/fauxssh](https://github.com/royans/fauxssh)

You can also try it out live! The honeypot is accessible at **blogofy.com** on standard port 22.
