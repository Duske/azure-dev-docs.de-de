---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: d4b9becdce2b78e928b97b7d980024eac5871df2
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369842"
---
Erstellen Sie neben der Klasse `DemoApplication` eine neue `Todo`-Java-Klasse:

```java
package com.example.demo;

import org.springframework.data.annotation.Id;

public class Todo {

    public Todo() {
    }

    public Todo(String description, String details, boolean done) {
        this.description = description;
        this.details = details;
        this.done = done;
    }

    @Id
    private Long id;

    private String description;

    private String details;

    private boolean done;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }
}
```

Bei dieser Klasse handelt es sich um ein Domänenmodell, das der zuvor erstellten `todo`-Tabelle zugeordnet ist.

Für die Verwaltung dieser Klasse ist ein Repository erforderlich. Definieren Sie eine neue `TodoRepository`-Schnittstelle im gleichen Paket:

```java
package com.example.demo;

import org.springframework.data.repository.reactive.ReactiveCrudRepository;

public interface TodoRepository extends ReactiveCrudRepository<Todo, Long> {
}
```

Dieses Repository ist ein reaktives Repository, das von Spring Data R2DBC verwaltet wird.

Stellen Sie die Anwendung fertig, indem Sie einen Controller erstellen, der Daten speichern und abrufen kann. Implementieren Sie eine `TodoController`-Klasse im gleichen Paket, und fügen Sie den folgenden Code hinzu:

```java
package com.example.demo;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@RestController
@RequestMapping("/")
public class TodoController {

    private final TodoRepository todoRepository;

    public TodoController(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    @PostMapping("/")
    @ResponseStatus(HttpStatus.CREATED)
    public Mono<Todo> createTodo(@RequestBody Todo todo) {
        return todoRepository.save(todo);
    }

    @GetMapping("/")
    public Flux<Todo> getTodos() {
        return todoRepository.findAll();
    }
}
```

Halten Sie schließlich die Anwendung an, und starten Sie sie erneut:

```bash
./mvnw spring-boot:run
```

## <a name="test-the-application"></a>Testen der Anwendung

Zum Testen der Anwendung können Sie cURL verwenden.

Erstellen Sie zunächst ein neues todo-Element in der Datenbank:

```bash
curl  --header "Content-Type: application/json" \
          --request POST \
          --data '{"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done": "true"}' \
          http://127.0.0.1:8080
```

Mit diesem Befehl sollte das erstellte Element zurückgegeben werden:

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}
```

Rufen Sie anschließend die Daten mithilfe einer neuen cURL-Anforderung ab:

```bash
curl http://127.0.0.1:8080
```

Mit dem folgenden Befehl wird die Liste der „todo“-Elemente zurückgegeben, einschließlich des von Ihnen erstellten Elements:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}]
```
