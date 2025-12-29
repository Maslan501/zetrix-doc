# Adding New Tools

#### Adding New Tools

1. **Define Tool in Source**

```typescript
// In appropriate file (e.g., zetrix-client.ts)
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "zetrix_new_tool") {
    const { param1 } = request.params.arguments as { param1: string };
    
    try {
      const result = await yourFunction(param1);
      return {
        content: [{
          type: "text",
          text: JSON.stringify(result, null, 2)
        }]
      };
    } catch (error) {
      return {
        content: [{
          type: "text",
          text: `Error: ${error.message}`
        }],
        isError: true
      };
    }
  }
});
```

2. **Register Tool**

```typescript
{
  name: "zetrix_new_tool",
  description: "What this tool does",
  inputSchema: {
    type: "object",
    properties: {
      param1: {
        type: "string",
        description: "Parameter description"
      }
    },
    required: ["param1"]
  }
}
```

3. **Add Tests**

```javascript
// In tests/test-new-tool.js
describe('New Tool', () => {
  it('should work correctly', async () => {
    const result = await client.callTool('zetrix_new_tool', { param1: 'test' });
    expect(result).toBeDefined();
  });
});
```

4. **Update Documentation**
   * Add to README.md
   * Update docs/EXAMPLES.md
   * Include in this GITDOC
5. **Submit Pull Request**
   * Clear description
   * Tests included
   * Documentation updated

***

#### Code Guidelines
