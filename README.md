# Messaging (Email, SMS, Chat)

The common messaging vocabulary -- messages across email, SMS, chat apps and push, and the
templates, conversations, phone numbers, senders and suppressions around them -- distilled
from the APIs that already exist. Part of
[Schema Commons](https://github.com/api-commons/schema-commons), published by the
[API Commons](https://apicommons.org).

Built from the providers tagged Messaging, Email, SMS, MMS, RCS, WhatsApp, CPaaS,
Omnichannel, Push, Push Notifications, Notifications, Transactional Email, Email API or Email
Deliverability in [APIs.io](https://apis.io) that publish their own OpenAPI. Only harvested
contracts count -- nothing API Evangelist generated. Every number below is a count of
providers.

## What the market already agrees on

<!-- commons:summary -->
| Concept | Expose it | Readable schema | Core properties (share) | Collection named |
| --- | ---: | ---: | --- | --- |
| message | 198 | 181 | id 67%, body 67%, from 55% | messages 143, email 33, sms 32, message 31 |
| template | 66 | 58 | id 74%, name 72%, body 57% | templates 54, template 17, message_templates 4, email_templates 3 |
| conversation | 71 | 63 | id 79% | conversations 46, threads 18, chats 13, conversation 12 |
| phone_number | 33 | 32 | number 81%, id 69%, capabilities 56%, country 50%, name 50%, status 50% | numbers 22, phone-numbers 11, phonenumbers 5, phone_numbers 4 |
| sender | 19 | 17 | id 82%, email 71%, name 59% | senders 12, identities 4, verified_senders 2, sender_identities 1 |
| suppression | 22 | 19 | reason 63%, address 53%, created_at 53% | suppressions 10, bounces 6, unsubscribes 5, blocklist 3 |

Cohort: 365 providers with harvested OpenAPI, 198 crosswalks. Of the providers exposing a message: 84% create, 70% list, 50% fetch, 27% update, 30% delete. Generated 2026-11-10 by build.py.
<!-- /commons:summary -->

Shares are of the providers with a readable resource schema for that concept. The
extended tier and the long tail of every concept are in [profile.yml](profile.yml).

One shape is worth knowing before mapping to it: a message's `to` is in only 44% of message
resources, below `from` (54%). Many providers return the message as *received* -- inbound
webhooks and conversation history -- where the recipient is the account itself and is left
out. Channel-agnostic providers carry it as `recipients` or `personalizations`, which the
aliases fold in.

## Artifacts

- [openapi.yml](openapi.yml) -- OpenAPI 3.1 of the shared operations. No one serves it; it
  is the shape to map to. Lints clean against the
  [naming ruleset](https://github.com/api-commons/schema-commons/blob/main/ruleset/schema-commons-naming.yaml).
- [schema/](schema) -- JSON Schema 2020-12 per concept; each property carries
  `x-commons-tier`, `x-commons-share`, `x-commons-providers` and the spellings seen.
- [crosswalk/](crosswalk) -- one per provider, common name -> their name, with the harvested
  spec and operations each mapping was read from. Twilio, Telnyx, Infobip, Bird and Resend
  map fully on messages; Postmark sends to `/email`, OneSignal to `/notifications`.
- [adopters.yml](adopters.yml) -- who exposes which concept, linked to their APIs.io page.
- [concepts.yml](concepts.yml) -- the only hand-written input.
- [census/](census) -- the cohort and the raw census the profile is built from.

## Decisions

These are calls, not measurements, recorded so they can be revisited as the method matures.

- **Email marketing, verification, finder and security, and message brokers are out.**
  Campaigns and lists belong to a marketing context; verification and finder are data
  products; and a message on a queue is infrastructure, not communication.
- **One message concept across channels.** Email, SMS, MMS, chat-app and push messages share
  `body`, `id` and `from` in their core; the channel is a property (`channel`, 39%), not a
  separate resource. Sending by action path (`/mail/send`, `/email/batch`,
  `/messages/send`) counts as creating messages.
- **Phone numbers are `/phone-numbers`, though 21 providers say `/numbers`.** The
  `phone-numbers` family has 19, and `numbers` means something else in every other context --
  the same call as `/cardholders` in card issuing.
- **A sender is `/senders`.** Twelve providers say `senders`; `identities` (four) is also
  an identity-and-access word.
- **Everything but the message `requires: [message]`.** A provider's `/templates` or
  `/threads` counts here only if it also sends messages.
- **AI-assistant chat turns are not messages.** A `/messages` that is `role` plus `content`
  in an LLM conversation is excluded (one provider in this cohort).

## Rebuild

```bash
../schema-commons/scripts/run.sh .
```

## License

Artifacts CC BY-NC-SA 4.0 ([LICENSE](LICENSE)); code Apache 2.0 ([LICENSE-CODE](LICENSE-CODE)).
