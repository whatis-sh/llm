# whAtIs.sh LLM Model

This directory contains the Large Language Model (LLM) configuration for the whAtIs.sh service. The model is designed to mimic the behavior of the Unix `whatis` command, providing brief descriptions of commands, functions, and programming terms.

## Directory Structure

```
llm/
├── Modelfile          # Ollama model definition and system prompt
└── README.md          # This documentation
```

## Model Overview

- **Base Model**: `llama3.2:3b`
- **Purpose**: Generate Unix `whatis`-style command descriptions
- **Response Format**: Brief, concise descriptions (1-2 sentences max)
- **Special Features**: 
  - Verbose mode support (`-v` flag)
  - Command origin identification (bash, Python, etc.)
  - Web search capability for detailed documentation

## Modelfile Configuration

The `Modelfile` contains the system prompt that trains the model to:

1. **Provide brief descriptions** similar to Unix `whatis` command output
2. **Include command origins** (e.g., bash, Cisco IOS, Python)
3. **Support verbose mode** when `-v` or `verbose` flags are provided
4. **Search web sources** for official documentation when needed
5. **Reject non-command inputs** and provide usage examples

### System Prompt Behavior

The model is configured to:
- Respond with maximum 2 sentences for basic queries
- Provide detailed examples and explanations in verbose mode
- Prioritize official sources (man pages, PyPi docs, etc.)
- Focus on being extremely concise and actionable
- Remind users of proper usage for invalid inputs

## Setup Instructions

### Prerequisites

- [Ollama](https://ollama.ai) installed and running
- At least 4GB of available RAM for the model

### Installation

1. **Start Ollama service** (if not already running):
   ```bash
   ollama serve
   ```

2. **Create the whatis.sh model**:
   ```bash
   cd llm/
   ollama create whatis.sh -f Modelfile
   ```

3. **Verify model creation**:
   ```bash
   ollama list
   ```
   You should see `whatis.sh` in the list of available models.

### Testing the Model

Test the model directly with Ollama:

```bash
# Basic command query
ollama run whatis.sh "ls"

# Verbose query
ollama run whatis.sh "grep -v"

# Programming function
ollama run whatis.sh "print()"

# Invalid input (should provide usage guidance)
ollama run whatis.sh "this is not a command"
```

### Expected Output Format

**Basic Query**:
```
Input: "ls"
Output: "Lists directory contents and file information. Origin: bash"
```

**Verbose Query**:
```
Input: "grep -v"
Output: "Global regular expression print - searches text patterns in files. 
The -v flag inverts the match, showing lines that don't match the pattern.

Examples:
  grep -v "error" logfile.txt    # Show lines without "error"
  grep -v "^#" config.conf       # Exclude comment lines
  
Origin: bash"
```

## Integration with API

The LLM model integrates with the FastAPI service located in `/api`. The API sends requests to the Ollama service:

```bash
# API makes this request to Ollama
curl -X POST http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "whatis.sh",
    "prompt": "ls",
    "stream": false
  }'
```

## Configuration

### Model Parameters

The model uses default Ollama parameters optimized for:
- **Fast response times**
- **Consistent output format**
- **Accurate command descriptions**

### Environment Variables

When using with the API service:
- `LLM_BASE_URL`: Ollama service URL (default: `http://localhost:11434`)
- `LLM_MODEL`: Model name (default: `whatis.sh`)

## Customization

To modify the model behavior:

1. **Edit the Modelfile** system prompt
2. **Recreate the model**:
   ```bash
   ollama create whatis.sh -f Modelfile
   ```
3. **Test changes** with sample queries
4. **Restart the API service** to use the updated model

### Example Customizations

- **Add new command sources**: Modify the system prompt to include additional origins
- **Change response length**: Adjust the sentence limit in the prompt
- **Add specialized domains**: Include specific technology stacks or tools

## Troubleshooting

### Common Issues

1. **Model not found**:
   - Ensure Ollama is running: `ollama serve`
   - Verify model creation: `ollama list`
   - Recreate if needed: `ollama create whatis.sh -f Modelfile`

2. **Slow responses**:
   - Check available system memory
   - Consider using a smaller base model
   - Verify Ollama service is healthy

3. **Inconsistent outputs**:
   - Review and refine the system prompt
   - Test with various input types
   - Consider adjusting model parameters

### Performance Optimization

- **Use appropriate hardware**: GPU acceleration if available
- **Monitor memory usage**: Ensure sufficient RAM for the model
- **Optimize prompts**: Keep system prompts focused and clear

## Development

### Testing New Prompts

1. Modify the `Modelfile`
2. Create a test version: `ollama create whatis-test -f Modelfile`
3. Test with various inputs
4. Update the main model when satisfied

### Contributing

1. Test changes thoroughly with diverse command types
2. Ensure backward compatibility with the API
3. Document any behavior changes
4. Submit pull requests with test examples

## Support

- **Model Issues**: Check Ollama logs and model responses
- **Integration Issues**: See `/api/README.md` for API troubleshooting
- **Performance Issues**: Monitor system resources and Ollama metrics
