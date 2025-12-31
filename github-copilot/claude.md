# Claude (Anthropic)

## Model Variants

- **Claude Opus** - Most capable, best for complex tasks
- **Claude Sonnet** - Balanced performance and speed
- **Claude Haiku** - Fastest, best for simple tasks

## Strengths

- **Excellent reasoning abilities** - Strong at breaking down complex problems
- **Code understanding** - Great at analyzing existing code and suggesting improvements
- **Long context window** - Can handle large codebases (up to 200K tokens)
- **Safety and helpfulness** - Tends to provide thoughtful, well-explained answers
- **Multi-step tasks** - Excellent at following complex, multi-step instructions
- **Documentation** - Very good at writing clear, detailed documentation
- **Refactoring** - Strong at suggesting code improvements and refactoring

## Weaknesses

- **Speed** - Opus can be slower than other models
- **Code generation** - Sometimes over-explains instead of just providing code
- **API knowledge** - May not be as up-to-date on latest framework versions
- **Creativity** - Can be more conservative in solutions compared to GPT-4

## Quirks

- Often provides detailed explanations even when you just want code
- May ask clarifying questions before providing a solution
- Tends to be more cautious about making assumptions
- Sometimes uses more verbose variable names and comments

## Best Prompt Style

### Do's

- **Be specific about what you want**: "Refactor this function to use async/await" works better than "improve this code"
- **Provide context**: Include relevant imports, type definitions, and surrounding code
- **Break down complex tasks**: "First, create the interface. Then, implement the class" 
- **Ask for explanations**: Claude excels at explaining "why" something works
- **Request step-by-step solutions**: "Walk me through how to implement X"

### Don'ts

- Don't be too vague - Claude will ask for clarification
- Avoid asking for just code snippets without context - you'll get explanations too
- Don't expect cutting-edge framework knowledge - verify API versions

### Example Prompts

Good:
```
I have a React component that fetches user data. Help me refactor it to use React Query 
for caching and add proper error handling. Here's my current component: [code]
```

Better:
```
Refactor this React component step by step:
1. Replace fetch with React Query's useQuery hook
2. Add error and loading states
3. Implement retry logic for failed requests
Here's my current code: [code]
```

## Best Use Cases

- Code reviews and analysis
- Refactoring and optimization
- Writing comprehensive documentation
- Debugging complex issues
- Learning and understanding concepts
- Architecture and design discussions
