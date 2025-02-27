<!--BEGIN_BANNER_IMAGE-->

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="/.github/banner_dark.png">
  <source media="(prefers-color-scheme: light)" srcset="/.github/banner_light.png">
  <img style="width:100%;" alt="The LiveKit icon, the name of the repository and some sample code in the background." src="https://raw.githubusercontent.com/livekit/agents/main/.github/banner_light.png">
</picture>

<!--END_BANNER_IMAGE-->

# LiveKit Agents

<!--BEGIN_DESCRIPTION-->

The Agent Framework is designed for building realtime, programmable participants
that run on servers. Use it to create conversational, multi-modal voice
agents that can see, hear, and understand.

<!--END_DESCRIPTION-->

The framework includes plugins for common workflows, such as voice activity detection and speech-to-text.

Agents integrates seamlessly with Cloud or self-hosted [LiveKit](https://livekit.io/) server, offloading job queuing and scheduling responsibilities to it. This eliminates the need for additional queuing infrastructure. Agent code developed on your local machine can scale to support thousands of concurrent sessions when deployed to a server in production.

> This SDK is currently in Developer Preview. During this period, you may encounter bugs and the APIs may change.
>
> We welcome and appreciate any feedback or contributions. You can create issues here or chat live with us in the [LiveKit Community Slack](https://livekit.io/join-slack).

## Docs & Guides

- [Overview](https://docs.livekit.io/agents/)
- [Quickstart](https://docs.livekit.io/agents/quickstart)
- [Working with plugins](https://docs.livekit.io/agents/plugins)
- [Deploying agents](https://docs.livekit.io/agents/deployment)

> [!NOTE]
> There are breaking API changes between versions 0.7.x and 0.8.x. Please refer to the [0.8 migration guide](0.8-migration-guide.md) for a detailed overview of the changes.

## Examples

- [Voice assistant](https://github.com/livekit/agents/tree/main/examples/voice-assistant): A voice assistant with STT, LLM, and TTS. [Demo](https://kitt.livekit.io)
- [Video publishing](https://github.com/livekit/agents/tree/main/examples/simple-color): A demonstration of publishing RGB frames to a LiveKit Room
- [STT](https://github.com/livekit/agents/tree/main/examples/speech-to-text): An agent that transcribes a participant's audio into text
- [TTS](https://github.com/livekit/agents/tree/main/examples/text-to-speech): An agent that publishes synthesized speech to a LiveKit Room

## Installation

To install the core Agents library:

```bash
pip install livekit-agents
```

Agents includes a set of prebuilt plugins that make it easier to compose together agents. These plugins cover common tasks like converting speech to text or vice versa and running inference on a generative AI model. You can install a plugin as follows:

```bash
pip install livekit-plugins-deepgram
```

The following plugins are available today:

| Plugin                                                                             | Features                        |
| ---------------------------------------------------------------------------------- | ------------------------------- |
| [livekit-plugins-anthropic](https://pypi.org/project/livekit-plugins-anthropic/)   | LLM                             |
| [livekit-plugins-azure](https://pypi.org/project/livekit-plugins-azure/)           | STT, TTS                        |
| [livekit-plugins-cartesia](https://pypi.org/project/livekit-plugins-cartesia/)     | TTS                             |
| [livekit-plugins-deepgram](https://pypi.org/project/livekit-plugins-deepgram/)     | STT                             |
| [livekit-plugins-elevenlabs](https://pypi.org/project/livekit-plugins-elevenlabs/) | TTS                             |
| [livekit-plugins-google](https://pypi.org/project/livekit-plugins-google/)         | STT, TTS                        |
| [livekit-plugins-nltk](https://pypi.org/project/livekit-plugins-nltk/)             | Utilities for working with text |
| [livekit-plugins-openai](https://pypi.org/project/livekit-plugins-openai/)         | LLM, STT, TTS                   |
| [livekit-plugins-silero](https://pypi.org/project/livekit-plugins-silero/)         | VAD                             |
| [livekit-plugins-anthropic](https://pypi.org/project/livekit-plugins-anthropic/)   | LLM                             |

## Using LLM models

Agents framework supports a wide range of LLMs and hosting providers.

### OpenAI-compatible models

Most LLM providers offer an OpenAI-compatible API, which can be used with the `livekit-plugins-openai` plugin.

```python
from livekit.plugins.openai.llm import LLM
```

- OpenAI: `LLM(model="gpt-4o")`
- Azure: `LLM.with_azure(azure_endpoint="", azure_deployment="")`
- Fireworks: `LLM.with_fireworks(api_key="", model="")`
- Groq: `LLM.with_groq(api_key="", model="")`
- OctoAI: `LLM.with_octo(api_key="", model="")`
- Ollama: `LLM.with_ollama(base_url="http://localhost:11434/v1", model="")`
- Perplexity: `LLM.with_perplexity(api_key="", model="")`
- TogetherAI: `LLM.with_together(api_key="", model="")`

### Anthropic Claude

Anthropic Claude can be used with `livekit-plugins-anthropic` plugin.

```python
from livekit.plugins.anthropic.llm import LLM

myllm = LLM(model="claude-3-opus-20240229")
```

## Concepts

- **Agent**: A function that defines the workflow of a programmable, server-side participant. This is your application code.
- **Worker**: A container process responsible for managing job queuing with LiveKit server. Each worker is capable of running multiple agents simultaneously.
- **Plugin**: A library class that performs a specific task, like speech-to-text, from a specific provider. An agent can compose multiple plugins together to perform more complex tasks.

## Running an agent

The framework exposes a CLI interface to run your agent. To get started, you'll need the following environment variables set:

- LIVEKIT_URL
- LIVEKIT_API_KEY
- LIVEKIT_API_SECRET

### Starting the worker

This will start the worker and wait for users to connect to your LiveKit server:

```bash
python my_agent.py start
```

To run the worker in dev-mode (with hot code reloading), you can use the dev command:

```bash
python my_agent.py dev
```

### Using playground for your agent UI

To ease the process of building and testing an agent, we've developed a versatile web frontend called "playground". You can use or modify this app to suit your specific requirements. It can also serve as a starting point for a completely custom agent application.

- [Hosted playground](https://agents-playground.livekit.io)
- [Source code](https://github.com/livekit/agents-playground)
- [Playground docs](https://docs.livekit.io/agents/playground)

### Joining a specific room

To join a LiveKit room that's already active, you can use the `connect` command:

```bash
python my_agent.py connect --room <my-room>
```

### What happens when I run my agent?

When you follow the steps above to run your agent, a worker is started that opens an authenticated WebSocket connection to a LiveKit server instance(defined by your `LIVEKIT_URL` and authenticated with an access token).

No agents are actually running at this point. Instead, the worker is waiting for LiveKit server to give it a job.

When a room is created, the server notifies one of the registered workers about a new job. The notified worker can decide whether or not to accept it. If the worker accepts the job, the worker will instantiate your agent as a participant and have it join the room where it can start subscribing to tracks. A worker can manage multiple agent instances simultaneously.

If a notified worker rejects the job or does not accept within a predetermined timeout period, the server will route the job request to another available worker.

### What happens when I SIGTERM a worker?

The orchestration system was designed for production use cases. Unlike the typical web server, an agent is a stateful program, so it's important that a worker isn't terminated while active sessions are ongoing.

When calling SIGTERM on a worker, the worker will signal to LiveKit server that it no longer wants additional jobs. It will also auto-reject any new job requests that get through before the server signal is received. The worker will remain alive while it manages any agents connected to rooms.

### Downloading model files

Some plugins require model files to be downloaded before they can be used. To download all the necessary models for your agent, execute the following command:

```bash
python my_agent.py download-files
```

If you're developing a custom plugin, you can integrate this functionality by implementing a `download_files` method in your Plugin class:

```python
class MyPlugin(Plugin):
    def __init__(self):
        super().__init__(__name__, __version__)

    def download_files(self):
        _ = torch.hub.load(
            repo_or_dir="my-repo",
            model="my-model",
        )
```

<!--BEGIN_REPO_NAV-->

<br/><table>

<thead><tr><th colspan="2">LiveKit Ecosystem</th></tr></thead>
<tbody>
<tr><td>Realtime SDKs</td><td><a href="https://github.com/livekit/components-js">React Components</a> · <a href="https://github.com/livekit/client-sdk-js">Browser</a> · <a href="https://github.com/livekit/components-swift">Swift Components</a> · <a href="https://github.com/livekit/client-sdk-swift">iOS/macOS/visionOS</a> · <a href="https://github.com/livekit/client-sdk-android">Android</a> · <a href="https://github.com/livekit/client-sdk-flutter">Flutter</a> · <a href="https://github.com/livekit/client-sdk-react-native">React Native</a> · <a href="https://github.com/livekit/rust-sdks">Rust</a> · <a href="https://github.com/livekit/node-sdks">Node.js</a> · <a href="https://github.com/livekit/python-sdks">Python</a> · <a href="https://github.com/livekit/client-sdk-unity-web">Unity (web)</a> · <a href="https://github.com/livekit/client-sdk-unity">Unity (beta)</a></td></tr><tr></tr>
<tr><td>Server APIs</td><td><a href="https://github.com/livekit/node-sdks">Node.js</a> · <a href="https://github.com/livekit/server-sdk-go">Golang</a> · <a href="https://github.com/livekit/server-sdk-ruby">Ruby</a> · <a href="https://github.com/livekit/server-sdk-kotlin">Java/Kotlin</a> · <a href="https://github.com/livekit/python-sdks">Python</a> · <a href="https://github.com/livekit/rust-sdks">Rust</a> · <a href="https://github.com/agence104/livekit-server-sdk-php">PHP (community)</a></td></tr><tr></tr>
<tr><td>Agents Frameworks</td><td><b>Python</b> · <a href="https://github.com/livekit/agent-playground">Playground</a></td></tr><tr></tr>
<tr><td>Services</td><td><a href="https://github.com/livekit/livekit">LiveKit server</a> · <a href="https://github.com/livekit/egress">Egress</a> · <a href="https://github.com/livekit/ingress">Ingress</a> · <a href="https://github.com/livekit/sip">SIP</a></td></tr><tr></tr>
<tr><td>Resources</td><td><a href="https://docs.livekit.io">Docs</a> · <a href="https://github.com/livekit-examples">Example apps</a> · <a href="https://livekit.io/cloud">Cloud</a> · <a href="https://docs.livekit.io/home/self-hosting/deployment">Self-hosting</a> · <a href="https://github.com/livekit/livekit-cli">CLI</a></td></tr>
</tbody>
</table>
<!--END_REPO_NAV-->
