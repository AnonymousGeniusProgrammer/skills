# Chapter 15: My Application Is All API Calls

Source: printed pp. 199-207; PDF pp. 222-230.

## Core idea

Find the application's decisions inside the API calls. Code that mostly invokes a framework still chooses sequence, conditions, transformations, and outputs; those responsibilities can often be separated and tested.

## Identify the computational core

Describe what the program does without reciting its API names. Break that description into responsibilities, then determine which require the external mechanism and which can operate on supplied data.

For each candidate ask what it needs, what it decides, and what effect it produces. A library message type can sometimes be constructed cheaply and used directly in a logic test; complete elimination of every library type is not a prerequisite for useful isolation.

## Choose an extraction strategy

| Strategy | How | Best fit | Main trade-off |
|---|---|---|---|
| **Skin and Wrap the API** | Mirror the used API operations in owned interfaces; delegate through thin wrappers | Small API; broad separation needed; little existing protection | More wrappers and API-shaped concepts to maintain |
| **Responsibility-Based Extraction** | Extract a coherent job and move it behind a higher-level boundary | Complex API; trustworthy extraction tools or a carefully manageable manual extraction | Application decisions can remain inside the extracted adapter and outside isolated tests |

Preserve signatures and delegation semantics when skinning. For responsibility-based extraction, check what behavior moved out of reach of the tests. Both strategies can coexist: a thin boundary supplies substitutability while a higher-level boundary expresses application intent.

## Worked example: mailing-list server

Reconstruction of the chapter's JavaMail example. The original code opens mail stores, reads messages, creates forwarded messages, changes headers and recipients, sends them, and sleeps before polling again.

Separate four responsibilities:

1. **Receive:** obtain incoming messages and pass them onward.
2. **Send:** transmit a supplied message.
3. **Forwarding policy:** derive an outgoing message and recipient list from an incoming message and roster.
4. **Drive:** determine when another check occurs.

A `MailReceiver` feeds a `MessageProcessor`; a `MailForwarder` builds messages and uses a `MailService`; a `MailSender` supplies the real transmission. A `ListDriver` controls periodic polling.

Now tests can give the forwarding logic a sample message and recording sender. Observe the generated sender, recipient list, subject marker, and other fields relevant to the change. Keep actual transmission and polling lifecycle checks at their own boundaries.

The important result is the separation of message policy from transport. It is not the exact class count or names from the example.

## Mechanically preserve behavior

When lifting a block that obtains a session, connects transport, and sends a message, retain arguments, order, exception behavior, and resource cleanup. A neat `sendMessage` name is not evidence that the extracted implementation does only transport work; inspect any filtering or formatting moved with it.

When wrapping a library object returned by another library object, trace the creation chain. Skinning one leaf can force additional adaptation. If that grows disproportionately, a larger responsibility boundary may be more economical.

## Anti-patterns

- "It only calls APIs, so there is nothing to test": ignores application choices encoded in those calls.
- Wrapping every available API operation: increases work beyond the needed collaboration.
- Moving all logic into a class named after a responsibility without making its inputs and effects controllable: changes organization but not testability.
- Replacing both the external action and the relevant policy with one fake: loses the behavior the test was meant to protect.

## Apply it

Produce a responsibility list, identify the part that can run with supplied data, and choose a boundary strategy with its testing gap stated. Demonstrate that the real application decision executes while the external action is substituted.

## Connects to

- [Ch 3](ch03-sensing-and-separation.md): observe interactions through a substitute.
- [Ch 14](ch14-library-dependencies.md): limit vendor coupling.
- [Ch 20](ch20-large-classes.md): discover and extract responsibilities.
