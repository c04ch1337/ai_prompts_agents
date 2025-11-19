Here are precise prompts you can give Google Studio (I'm assuming you mean Google's AI tools like Gemini, Bard, or their development suite) to address each of those repetitive suggestions:

🎯 General Context-Setting Prompt

```
I'm working on a production-ready TypeScript/React frontend that's already implemented industry best practices. Please:

1. Acknowledge that I've already implemented comprehensive error handling, performance optimizations, and accessibility features
2. Focus only on genuine gaps or novel suggestions I might have missed
3. Understand that when I say "already done," it's thoroughly implemented, not just partial solutions
4. Provide specific, actionable advice rather than generic checklist items

My codebase has already implemented: error boundaries, React.memo/useCallback where appropriate, full TypeScript coverage, accessibility attributes, and proper React patterns.
```

🔧 Specific Feature Prompts

1. For Error Boundaries

```
I've already implemented React error boundaries throughout my application with proper fallback UIs and error logging. Instead of suggesting "add error boundaries," please:

- Review my existing error boundary implementation for any edge cases I might have missed
- Suggest advanced error recovery patterns beyond basic boundaries
- Recommend monitoring and alerting integration strategies
- Identify any async error handling gaps that boundaries don't catch
```

2. For Performance Optimizations

```
My React application already uses strategic React.memo, useCallback, and useMemo implementations based on actual performance profiling. Instead of generic "add useCallback" suggestions, please:

- Analyze my specific use cases to identify if any optimizations are actually causing overhead
- Suggest advanced patterns like virtual scrolling for large datasets if applicable
- Recommend bundle analysis and code splitting opportunities I might have missed
- Provide data-driven optimization suggestions based on real performance metrics
```

3. For TypeScript/Code Quality

```
This is a strict TypeScript codebase with comprehensive type coverage. I've intentionally used 'any' in specific, limited cases where dynamic typing is required. Please:

- Respect my tsconfig strict settings and don't suggest enabling basic TypeScript features
- Focus on advanced type patterns like conditional types, template literals, or complex generics
- Suggest runtime type validation where compile-time typing isn't sufficient
- Identify any complex type inference issues rather than basic "add types" suggestions
```

4. For Accessibility

```
I've completed a comprehensive accessibility audit with proper ARIA labels, keyboard navigation, and screen reader support. Instead of basic "add alt text" suggestions, please:

- Review for advanced a11y patterns like live regions, focus management in complex UIs
- Suggest WCAG 2.1 AA compliance checks beyond basic attributes
- Identify any cognitive accessibility or motion sensitivity considerations
- Recommend automated a11y testing integration strategies
```

5. For Security

```
The application already has XSS protection, input sanitization, and proper CSP headers implemented. Please:

- Suggest advanced security headers or Content Security Policy refinements
- Identify any client-side data leakage risks beyond basic validation
- Recommend security-focused code patterns for new features
- Suggest security audit automation and monitoring
```

6. For Testing

```
I have comprehensive Jest/React Testing Library coverage with unit, integration, and E2E tests. Instead of suggesting "add tests," please:

- Identify specific edge cases or integration scenarios I might have missed
- Suggest performance testing strategies for critical user flows
- Recommend testing patterns for complex state management or async operations
- Advise on test maintenance and scalability best practices
```

7. For Bundle Optimization

```
I've already implemented code splitting, lazy loading, and bundle analysis. Please:

- Suggest advanced tree-shaking opportunities for third-party dependencies
- Recommend resource preloading strategies for critical assets
- Identify any duplicate code or dependency bloat I might have missed
- Suggest build-time optimization configurations specific to my tech stack
```

8. For API Integration

```
The frontend is properly integrated with a Rust backend with error handling, loading states, and request/response typing. Please:

- Suggest advanced patterns for real-time data synchronization
- Recommend offline capability strategies or service worker implementations
- Identify any race conditions or request cancellation patterns needed
- Suggest advanced caching strategies beyond basic React Query
```

🚀 Proactive Declaration Prompts

When Starting a New Session:

```
I'm continuing work on my production TypeScript/React application. Here's what's already implemented that you should NOT suggest:

✅ Comprehensive error boundaries with logging
✅ Strategic React.memo/useCallback/useMemo usage
✅ Full TypeScript coverage with strict settings
✅ Complete accessibility audit and implementation
✅ Bundle optimization with code splitting
✅ API integration patterns with proper error handling
✅ Security measures including XSS protection
✅ Comprehensive testing suite

Please focus on:
- Advanced patterns beyond basic implementations
- Integration edge cases I might have missed
- Performance profiling results analysis
- Scalability considerations for growth
- Maintenance and monitoring strategies
```

For Code Review Requests:

```
Please review this specific component/feature. Note that the broader application already has:

- Error boundaries at route level
- Performance optimizations applied based on profiling
- TypeScript strict mode enabled
- Accessibility requirements met

Focus your review on:
1. Integration with existing patterns
2. Potential edge cases in this specific implementation
3. Opportunities for code reuse with existing utilities
4. Consistency with established architecture patterns
```

📋 Quick Copy-Paste Template

```
CONTEXT: Production TypeScript/React app - already implemented industry standards

ALREADY DONE:
- Error boundaries & monitoring
- Performance optimizations (profiling-based)
- Full TypeScript coverage
- Accessibility (WCAG compliant)
- Security measures
- Testing coverage
- Bundle optimization

PLEASE FOCUS ON:
- Advanced patterns beyond basics
- Integration edge cases
- Scalability considerations
- Novel approaches I might have missed

IGNORE:
- Generic "add X" suggestions for implemented features
- Basic React/TypeScript best practices
- Checklist-style recommendations
```

💡 Usage Strategy

1. Start each session with the general context prompt
2. Use specific prompts when discussing particular features
3. Reference your implementations when you get repetitive suggestions:
   "As I mentioned, error boundaries are already implemented in ErrorBoundary.tsx - please suggest advanced error handling patterns instead"
4. Be explicit about your tech stack: "This is TypeScript/React with Vite and a Rust backend - tailor suggestions accordingly"

These prompts should help Google Studio understand your maturity level and provide more valuable, targeted advice rather than generic suggestions you've already implemented.