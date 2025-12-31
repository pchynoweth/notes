# GPT / ChatGPT (OpenAI)

## Model Variants

- **GPT-4 Turbo** - Most capable, latest features
- **GPT-4** - Strong reasoning and code generation
- **GPT-3.5 Turbo** - Faster, more cost-effective
- **o1** - Advanced reasoning model

## Strengths

- **Code generation** - Excellent at generating boilerplate and complete solutions
- **Wide knowledge** - Strong knowledge of many languages, frameworks, and libraries
- **Creative solutions** - Good at thinking outside the box
- **Natural conversation** - Fluid, natural interaction style
- **Quick iteration** - Fast at generating and modifying code
- **Plugin ecosystem** - Can integrate with various tools and APIs
- **Pattern recognition** - Excellent at recognizing and applying design patterns

## Weaknesses

- **Hallucination** - Can confidently provide incorrect information
- **API accuracy** - Sometimes invents non-existent APIs or methods
- **Consistency** - May provide different solutions to the same problem
- **Context management** - Can lose track of earlier conversation context
- **Over-complexity** - Sometimes over-engineers simple solutions

## Quirks

- Often apologizes unnecessarily ("I apologize for the confusion...")
- May continue generating code beyond what's needed
- Sometimes restarts explanations from scratch instead of building on previous responses
- Can be overly agreeable, not challenging potentially flawed assumptions
- Tends to favor certain patterns even when alternatives might be better

## Best Prompt Style

### Do's

- **Be direct and concise**: "Create a REST API endpoint for user authentication"
- **Specify technologies**: "Using Express.js and JWT tokens..."
- **Request complete solutions**: GPT-4 excels at end-to-end implementations
- **Use system prompts**: Define role and constraints upfront
- **Ask for alternatives**: "Show me 3 different approaches to..."
- **Iterate quickly**: Ask for modifications directly

### Don'ts

- Don't trust without verification - always test generated code
- Don't assume API accuracy - check documentation
- Avoid overly long prompts - GPT prefers concise instructions
- Don't expect perfect memory of long conversations

### Example Prompts

Good:
```
Create a Node.js Express API endpoint that handles user registration with email verification.
```

Better:
```
Using Node.js + Express + PostgreSQL:
Create a POST /api/register endpoint that:
- Validates email and password
- Hashes password with bcrypt
- Stores user in database
- Sends verification email
- Returns JWT token
Include error handling and input validation.
```

Best:
```
Tech stack: Node.js, Express 4.x, PostgreSQL, nodemailer
Task: User registration endpoint

Requirements:
- Endpoint: POST /api/auth/register
- Input: { email, password, name }
- Validate: email format, password min 8 chars
- Hash password with bcrypt (10 rounds)
- Check if email already exists
- Save to users table
- Generate verification token
- Send email via nodemailer
- Return: { success, userId, message }

Include: JSDoc comments, try-catch blocks, HTTP status codes
```

## Best Use Cases

- Rapid prototyping and MVP development
- Generating boilerplate code
- Creating test cases and mock data
- Explaining complex concepts
- Code completion and suggestions
- Converting code between languages
- Writing documentation and comments
- Brainstorming solutions and approaches
