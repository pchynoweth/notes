# Gemini (Google)

## Model Variants

- **Gemini Ultra** - Most capable model
- **Gemini Pro** - Balanced performance
- **Gemini Nano** - Lightweight for on-device use

## Strengths

- **Multimodal capabilities** - Can process text, images, audio, and video
- **Fast responses** - Generally quick response times
- **Google integration** - Strong with Google Cloud Platform and services
- **Code execution** - Can run and test code snippets internally
- **Reasoning** - Good at logical reasoning and problem-solving
- **Large context** - Supports long context windows
- **Up-to-date knowledge** - Well-informed about recent technologies

## Weaknesses

- **Consistency** - Can sometimes provide inconsistent responses
- **Complex refactoring** - May struggle with large-scale code refactoring
- **Verbose** - Can be overly detailed in explanations
- **Edge cases** - Sometimes misses edge cases in code generation
- **Package versions** - Occasionally provides outdated package versions (verify versions)

## Quirks

- May provide multiple alternative solutions without clear recommendation
- Sometimes mixes different coding styles in the same response
- Can be overly enthusiastic about suggesting new features
- Occasionally provides outdated package versions

## Best Prompt Style

### Do's

- **Be explicit about constraints**: "Using Python 3.10, create a function that..."
- **Request specific formats**: "Provide the solution as a single code block"
- **Mention Google services explicitly**: Gemini is strong with GCP, Firebase, etc.
- **Ask for testing**: "Include unit tests for this function"
- **Specify language/framework versions**: This helps with accuracy

### Don'ts

- Don't ask open-ended questions without context
- Avoid assuming it knows your project structure
- Don't expect it to maintain state across separate conversations well

### Example Prompts

Good:
```
Write a Python function that processes CSV files and uploads them to Google Cloud Storage.
Include error handling for network issues.
```

Better:
```
Using Python 3.10 and google-cloud-storage library:
1. Create a function that reads a CSV file
2. Validates the data format
3. Uploads to GCS bucket with retry logic
4. Returns success/failure status
Include type hints and docstrings.
```

## Best Use Cases

- Google Cloud Platform integrations
- Multimodal applications (image/video processing)
- Quick prototyping and code generation
- Data analysis and processing
- Firebase and Google services integration
- Testing and validation code
