# ioa

Complex tasks can require several AI agents with different tools and skills. ioa is a Python framework that connects distributed agents so they can form teams, exchange messages, and work on a shared task.

## Features

- Connect agents across different environments through an internet-inspired architecture.
- Form teams and nested sub-teams automatically as tasks require.
- Integrate different agent implementations, including AutoGPT and Open Interpreter.
- Execute agent tasks asynchronously.
- Manage conversation flow automatically during agent collaboration.

## Installation

The documented setup uses Docker rather than a Python package install. Install Docker with Docker Compose support, and have Python available if you want to run the evaluation script.

From a local checkout of the ioa source:

```bash
cd ioa
python -m venv .venv
source .venv/bin/activate
```

Build the core service images:

```bash
docker build -f dockerfiles/server.Dockerfile -t ioa-server:latest .
docker build -f dockerfiles/client.Dockerfile -t ioa-client:latest .
docker build -f dockerfiles/server_frontend.Dockerfile -t ioa-server-frontend:latest .
```

Build the agent images used by the example configuration:

```bash
docker build -f dockerfiles/tool_agents/autogpt.Dockerfile -t autogpt:latest .
docker build -f dockerfiles/tool_agents/open_interpreter.Dockerfile -t open-interpreter:latest .
```

Create the shared network and start Milvus:

```bash
docker network create agent_network
docker-compose -f dockerfiles/compose/milvus.yaml up
```

Leave Milvus running and open another terminal for the next steps. If your Docker installation uses Compose V2, replace `docker-compose` with `docker compose`.

## Usage

Copy the environment template from the repository root:

```bash
cp dockerfiles/compose/.env_template dockerfiles/compose/.env
```

Edit `dockerfiles/compose/.env` and set your OpenAI API key. Review the other settings before you start the services.

**Keep API keys out of version control.** Agent runs can make paid model requests. Review the agent configuration and available tools before you give agents access to local files or external services.

Start the AutoGPT and Open Interpreter example:

```bash
docker-compose -f dockerfiles/compose/open_instruction.yaml up
```

This configuration starts a small agent network with the two agent implementations. Once the services are ready, the included Open Instruction evaluation script can submit tasks:

```bash
python scripts/open_instruction/test_open_instruction.py
```

The evaluation script requires its dataset and Python dependencies to be available in your environment; creating a virtual environment alone does not install them.

Stop the example services with:

```bash
docker-compose -f dockerfiles/compose/open_instruction.yaml down
```

## License

ioa is licensed under Apache-2.0 and is based on [OpenBMB/IoA](https://github.com/OpenBMB/IoA). See `LICENSE` for the license terms. Retain applicable upstream copyright and attribution notices, including any supplied `NOTICE` file, when redistributing the code.
