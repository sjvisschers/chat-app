# Workshop
```
ng generate component chat
ng generate component chat/chat-list
ng generate component chat/chat-form
ng serve
```

#### 1.1 Displaying ChatComponent
```
<ws-chat></ws-chat>
```
The selector above is now available throughout the application.
```
Replace the content of AppComponent's template with a ChatComponent.
```
Before we continue test the application.
```
ng test
```
It seems that the change to AppComponent's template broke the unit tests.
```
app.component.spec.ts
Add ChatComponent, ChatComponentList, ChatComponentForm to the declarations of the TestBed.
Remove the unit tests checking for the title and h1 element.
```

#### 1.2 Displaying ChatList and ChatForm
```
<ws-chat-list></ws-chat-list>
<ws-chat-form></ws-chat-form>
```
```
Add ChatList and ChatForm to the template of ChatComponent
```

#### 1.3 Implementing ChatForm
ChatForm is supposed to be a "dumb" component so it will communicate to its parent with events.
The (ngSubmit) directives emits a send event containing the value of the local template variable #inputRef.
```
import { Component, OnInit, EventEmitter, Output } from '@angular/core';

@Component({
    selector: 'ws-chat-form',
    templateUrl: './chat-form.component.html',
    styleUrls: ['./chat-form.component.scss']
})
export class ChatFormComponent implements OnInit {
    @Output() send: EventEmitter<string>
        = new EventEmitter<string>();

    constructor() {
    }

    ngOnInit() {
    }
}

```
```
<form (ngSubmit)="send.emit(inputRef.value)">
  <input #inputRef/>
</form>
```

#### 1.4 Listening to ChatForm
Chat is a "smart" component and is aware of ChatList and ChatForm.
```
Create a method in ChatComponent called onSend(message: string).
Listen to the send event of ChatForm by assigning it onSend($event).
```
```
import { Component } from '@angular/core';

@Component({
  selector: 'ws-chat',
  template: `
        <ws-chat-list></ws-chat-list>
        <ws-chat-form (send)="onSend($event)"></ws-chat-form>
    `
})
export class ChatComponent {
  onSend(content: string): void {
    console.log(content);
  }
}
```

#### 1.5 Keeping track of ChatMessages
Currently there's no model for the data.
```
Create a file called chat-message.model.ts in app/chat/shared.
Declare class ChatMessage
```
```
export class ChatMessage {
    constructor(private content: string) {}
}
```

```
Create a property inside ChatComponent called messages of type Array<ChatMessage>.
Implement onSend by creating a new ChatMessage and pushing it to the messages.
```

```
import { Component, OnInit } from '@angular/core';
import { ChatMessage } from './shared/chat-message.model';

@Component({
  selector: 'ws-chat',
  templateUrl: './chat.component.html',
  styleUrls: ['./chat.component.scss']
})
export class ChatComponent implements OnInit {
  messages: Array<ChatMessage> = [];
  constructor() { }

  ngOnInit() {
  }
  onSend(content: string): void {
    this.messages.push(new ChatMessage(content));
  }
}
```
```
<ws-chat-list></ws-chat-list>
<ws-chat-form (send)="onSend($event)"></ws-chat-form>
```
#### 1.6 Preparing ChatList
It's ChatList's responsibility to display ChatMessages.
```
Create an input variable in ChatList called messages of type Array<ChatMessage>.
Display the chat messages by looping through them with help of *ngFor.
```

```
import { Component, Input } from '@angular/core';

@Component({
    selector: 'ws-chat-list',
    template: `
        <div *ngFor="let message of messages">
           {{ message.content }}
        </div>
    `
})
export class ChatListComponent {
    @Input() messages: Array<ChatMessage>;
}
```

#### 1.7 Data-bind ChatList
ChatComponent retrieves ChatMessages and binds them to ChatList through a single way bind.

```
import { Component, OnInit } from '@angular/core';
import { ChatMessage } from './shared/chat-message.model';

@Component({
  selector: 'ws-chat',
  templateUrl: './chat.component.html',
  styleUrls: ['./chat.component.scss']
})
export class ChatComponent implements OnInit {
  messages: Array<ChatMessage> = [];
  constructor() { }

  ngOnInit() {
  }
  onSend(content: string): void {
    this.messages.push(new ChatMessage(content));
  }
}

```
```
<ws-chat-list [messages]="messages"></ws-chat-list>
<ws-chat-form (send)="onSend($event)"></ws-chat-form>
```

#### Bonus
```
Create a submit handler for ChatForm that emits the send event and clears the input (HTMLInputElement).
```