# Learning GraphQL


### GraphQL types
- Int
- Float
- String
- Booleans
- ID (unique identifiers)


### Querys

**Fragments**

- 여러 작업에서 재사용할 수 있는 selection set

```
query {
    Lift(id: "jazz-cat") {
      ...liftInfo
      trailAccess {
        ...trailInfo
      }
    }
    Trail(id: "river-run") {
      ...trailInfo
      groomed
      trees
      night
    }
}

fragment trailInfo on Trail {
  name
  difficulty
  accessedByLifts {
    ...liftInfo
  }
}

fragment liftInfo on Lift {
  name
  status
  capacity
  night
  elevationGain
}
```

**Union types**

- 다른 object types의 결합


### Mutations

- 새로운 데이터를 쓰기 위해서는 `mutations`를 사용해야됨.

```
mutation createSong {
  addSong(title:"No Scrubs", numberOne: true, performerName:"TLC") {
    id
    title
    numberOne
  }
}
```

### Subscriptions

- GraphQL API가 리얼타임으로 데이터 변화를 서버에서 푸시

```
subscription {
  liftStatusChange {
    name
    capacity
    status
  }
}
```

### Introspection

- GraphQL은 내부의 스키마또한 사용가능
- 그래서 QraphQL playground 등에서 docs, schema 정의가능

```
query {
  __schema {
    types {
      name
      description
    }
  }
}
```
