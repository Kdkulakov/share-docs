# answer-me-with-html: HTML and Video with Codex, Cursor, or Claude Code

The workflow: **install the skill → configure ElevenLabs → ask the agent to explain a topic → ask for a video**.

Describe your topic and preferences in chat. The agent writes the script, runs the skill's tools, creates the files, and checks the result. This guide covers macOS with zsh.

## 1. Install the skill

Run the command for your agent in the terminal:

```zsh
# Codex
npx -y skills add QingYunA/answer-me-with-html --skill answer-me-with-html -g -a codex -y

# Cursor
npx -y skills add QingYunA/answer-me-with-html --skill answer-me-with-html -g -a cursor -y

# Claude Code
npx -y skills add QingYunA/answer-me-with-html --skill answer-me-with-html -g -a claude-code -y
```

Run only the command that matches your agent. The `-g` flag installs the skill for your user account, making it available across projects. Start a new agent session after installation.

If `npx` is not found, ask the agent to check your Node.js and npm installation. If you have Homebrew, you can install them with:

```zsh
brew install node
```

Installation source: [answer-me-with-html](https://github.com/QingYunA/answer-me-with-html). Installer options: [skills CLI](https://github.com/vercel-labs/skills).

## 2. Ask the agent to check dependencies

Send this message in chat:

```text
Check the installed answer-me-with-html skill and the dependencies
needed to generate HTML and MP4 videos. Use tools that are already installed.
If anything is missing, explain what needs to be installed and why.
```

What the skill uses:

| Task | Requirements on your machine |
| --- | --- |
| HTML | Node.js 20+ |
| ElevenLabs narration | An API key, an available voice, and API access |
| MP4 export | Node.js 22+ according to the skill documentation, FFmpeg, and a supported browser |

**In our setup:** Chrome and FFmpeg were already installed. Codex used them automatically; we did not install Chrome separately. For export, the agent used the existing Node.js 20 installation with experimental WebSocket support. For a new setup, using Node.js 22+ is simpler.

The exporter uses the browser to render frames from the animated HTML page. FFmpeg combines those frames and the audio into an MP4. You do not need to open the browser manually for this step. An HTML player without MP4 export does not require this export step.

## 3. Add the ElevenLabs variables to `~/.zshrc`

In your ElevenLabs account, create an API key with access to speech synthesis and copy the Voice ID of your chosen voice.

Open your zsh configuration:

```zsh
nano ~/.zshrc
```

Add these three lines, replacing the placeholders with your own values:

```zsh
export ELEVENLABS_API_KEY='YOUR_API_KEY'
export ELEVENLABS_VOICE_ID='YOUR_VOICE_ID'
export ELEVENLABS_MODEL_ID='eleven_multilingual_v2'
```

Save the file and apply the settings in your current terminal:

```zsh
source ~/.zshrc
```

- `ELEVENLABS_API_KEY` — your API access key.
- `ELEVENLABS_VOICE_ID` — the voice identifier.
- `ELEVENLABS_MODEL_ID` — the speech synthesis model; we used `eleven_multilingual_v2` for our narration.

Enter the key in your editor; do not paste it into chat or project files. See [ElevenLabs API keys](https://elevenlabs.io/docs/api-reference/authentication) and [models](https://elevenlabs.io/docs/overview/models).

## 4. Choose a topic and get an HTML explanation

All examples below are **chat messages**, not terminal commands.

In Codex, start your prompt with `$answer-me-with-html`. In Claude Code, use `/answer-me-with-html`. In Cursor, you can write "Use the answer-me-with-html skill."

If you want the agent to suggest a topic:

```text
Use the answer-me-with-html skill.
Suggest three MLOps topics that would work well with diagrams.
I will choose one, then you can prepare a visual HTML explanation.
```

If you already have a topic, as in our Codex example:

```text
$answer-me-with-html Explain in Russian how GitLab Model Registry works.
Describe its components and how they interact.
Use examples from the current project and verify the facts against the documentation.
Choose a suitable visual theme and create an HTML page with diagrams.
Save the file in the project and provide a link to open it.
```

To add narration to the page:

```text
Add Russian narration using ElevenLabs and an audio player to the HTML explanation.
```

The agent prepares the text and embeds the audio. Adding narration to a regular HTML page is an extra task for the agent; the skill's built-in narration mode is designed for video.

## 5. Ask for a video

After receiving the HTML explanation, continue in the same conversation:

```text
Now generate a video based on this explanation using
the answer-me-with-html skill.
Include animated diagrams and Russian narration from ElevenLabs.
Save an MP4 and an HTML player. Check that labels and subtitles are readable.
```

You can specify your preferences:

```text
Make the explanation about four minutes long, with six scenes.
Use a dark visual theme. Explain everything in plain language.
```

The agent prepares the script, narration, animation, and export. You receive links to the MP4 and HTML player.

To request a correction, send a follow-up message:

```text
The subtitles overlap the labels in the third scene.
Fix the layout and rebuild the video, reusing the existing narration.
```

## 6. Troubleshooting

| Situation | What to ask the agent |
| --- | --- |
| The skill cannot be found | "Check the answer-me-with-html installation and the path to SKILL.md." |
| There is no narration | "Check the ElevenLabs connection, API key permissions, and access to the voice; do not display the key." |
| Only HTML was generated | "Export an MP4 as well and check the export dependencies." |
| A browser or FFmpeg is missing | "Check the tools already installed and suggest installing only the missing ones." |
| The diagram is too small | "Increase the label size, split the diagram into scenes, and check the frames." |

If the agent environment separately asks for permission to send text to ElevenLabs, that request concerns sending the narration script to an external service. In our conversation, permission was required for a description of the local project.

Skill documentation: [Codex](https://developers.openai.com/codex/skills/), [Cursor](https://cursor.com/docs/skills), [Claude Code](https://code.claude.com/docs/en/skills).
