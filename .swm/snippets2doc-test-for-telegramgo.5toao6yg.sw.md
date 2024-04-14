---
title: Snippets2Doc test for telegram.go
---
# Introduction

This document will walk you through the implementation of the Telegram message handling feature in our application. This feature allows our application to interact with users through Telegram, a popular messaging platform.

We will cover:

1. How the application handles incoming messages from Telegram.


2. How the application responds to users who have not set their Telegram user ID.


3. How the application handles memo relations in the context of Telegram messages.

# Handling incoming Telegram messages

<SwmSnippet path="/server/integration/telegram.go" line="50">

---

The first part of our implementation involves receiving and processing incoming messages from Telegram. This is done in the MessageHandle function in the server/integration/telegram.go file. This function takes in a context, a reference to the Telegram bot, the incoming message, and any attachments that come with the message. The function then sends a reply to the incoming message. This is the first point of interaction between our application and the user on Telegram.

```go
func (t *TelegramHandler) MessageHandle(ctx context.Context, bot *telegram.Bot, message telegram.Message, attachments []telegram.Attachment) error {
	reply, err := bot.SendReplyMessage(ctx, message.Chat.ID, message.MessageID, workingMessage)
```

---

</SwmSnippet>

# Handling users without a Telegram user ID

<SwmSnippet path="/server/integration/telegram.go" line="71">

---

In some cases, a user might send a message to our application without having set their Telegram user ID in their user settings. This is a problem because our application uses the Telegram user ID to identify the user. To handle this, we check if the creatorID is 0, which indicates that the user has not set their Telegram user ID. If this is the case, we send a message back to the user asking them to set their Telegram user ID. This ensures that all users interacting with our application through Telegram can be properly identified.

```go
	if creatorID == 0 {
		_, err := bot.EditMessage(ctx, message.Chat.ID, reply.MessageID, fmt.Sprintf("Please set your telegram userid %d in UserSetting of memos", message.From.ID), nil)
		return err
	}
```

---

</SwmSnippet>

# Handling memo relations

<SwmSnippet path="/server/integration/telegram.go" line="351">

---

The last part of our implementation involves handling memo relations. Memos are a key part of our application, and users can create relations between different memos. When a user sends a message related to a memo, we need to update the relations for that memo. This is done in the loop where we iterate over the relationList and append each relation to the payload.Memo.RelationList. This ensures that all memo relations are updated in response to the user's message.

```go
	for _, relation := range relationList {
		payload.Memo.RelationList = append(payload.Memo.RelationList, &webhook.MemoRelation{
			MemoID:        relation.MemoID,
			RelatedMemoID: relation.RelatedMemoID,
			Type:          string(relation.Type),
		})
	}
```

---

</SwmSnippet>

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBbWVtb3MlM0ElM0FlZGVuaGVybWVsaW4=" repo-name="memos"><sup>Powered by [Swimm](https://swimm-web-app.web.app/)</sup></SwmMeta>
