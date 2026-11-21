# Codex prompt: Club chatbot with Supabase memory

Build a Google Colab notebook for a student club chatbot based on the existing geography chatbot. The chatbot must stay on the club selected in the interface, remember conversations in Supabase after a Colab runtime restart, and provide a Gradio interface.

## Topics and behavior

- Provide these topics: Astronomy, Cooking, Football, World Geography, and Python Programming.
- The current topic is selected by the student. The bot answers only questions about that topic.
- Politely refuse off-topic questions and suggest changing the topic selector. This includes questions about a club that was selected earlier in the same conversation.
- Switching the topic keeps the same conversation and its messages, but all later answers follow the newly selected topic.

## LangGraph implementation

- Install `langgraph`, `langchain`, `langchain-openai`, `langgraph-checkpoint-postgres`, `psycopg[binary]`, and `gradio` in Colab.
- Read `OPENAI_API_KEY` and `SUPABASE_DB_URL` from `google.colab.userdata`; never print or commit either secret.
- Create a `PostgresSaver` with a psycopg connection using `autocommit=True`, `row_factory=dict_row`, and `prepare_threshold=0`. Call `checkpointer.setup()` and compile the graph with this saver.
- State contains `messages` using the `add_messages` reducer and a plain `topic` field. Do not store the system message in state.
- Use `init_chat_model("openai:gpt-6-luna")` and one node named `chatbot`. On every call, build a fresh `SystemMessage` from `state["topic"]`, call the model with `[SYSTEM] + state["messages"]`, and return only the model reply in the messages update.
- Keep the graph to `START -> chatbot -> END`. Include a graph drawing using `draw_mermaid_png()`.
- Each conversation has a UUID `thread_id`. Invoke the graph with only the new `HumanMessage` plus the current topic and the matching `thread_id` config; rely on the checkpointer for prior messages, not a Python history variable.

## Conversation records and Gradio interface

- Keep readable conversation metadata in a database table: `thread_id`, current `topic`, a short preview from the first user message, and an updated timestamp. Load the saved-conversation list from the database on app load and after changes; do not keep the list only in a Python variable.
- Provide a topic dropdown, chat window, message box, Send action, New conversation button, and saved-conversations dropdown.
- New conversation creates a fresh UUID thread and clears the visible chat.
- Selecting a saved conversation loads its topic and messages from the database/checkpointer into the interface.
- Changing the topic updates the topic for the same thread without clearing messages.
- Label each saved conversation with its topic and a short, readable preview rather than only its UUID.
- Launch the interface from the notebook. Warn that a public Gradio share link can let other people use the configured API key; do not put secrets in the notebook or its outputs.

## Student verification

Include a manual test cell that demonstrates an on-topic answer, a polite off-topic refusal, a topic switch in the same thread, and refusal of a question about the previous topic. Show how to inspect `graph.get_state(config).values`. The notebook instructions should have the student restart the Colab runtime and reopen a saved conversation to verify Supabase persistence.
