# Contributing

This project is open for improvements, adaptations, and forks.

## Ways to Contribute

- **Adapting for your facility** — fork the repo, adjust escalation contacts and severity rules
- **Reporting bugs** — open a GitHub Issue with screenshots and error messages
- **Suggesting features** — open a GitHub Issue with the `enhancement` label
- **Sharing your deployment** — open a Discussion describing how you adapted it

## Roadmap Contributions Welcome

- [ ] WhatsApp alerts via Twilio
- [ ] Weekly PDF summary report via email
- [ ] Power BI dashboard template
- [ ] DATIX integration for NHS trusts
- [ ] Multi-site / multi-department support
- [ ] Escalation reminder if CRITICAL unacknowledged after 30 minutes
- [ ] Twilio SMS fallback when email fails

## Adapting the Severity Rules

Edit the system prompt in Make.com's OpenAI module to match your facility's classification framework.

For NHS trusts, align with the **NHS Serious Incident Framework** categories:
- Never Events → CRITICAL
- Serious Incidents → HIGH
- Incidents requiring investigation → MEDIUM
- Near misses / no harm → LOW

## Licence

MIT. No attribution required, though appreciated.
