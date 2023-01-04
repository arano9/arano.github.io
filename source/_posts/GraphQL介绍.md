---
layout: post
title: GraphQL介绍
date: 2023-01-03 18:16:53
categories: note
tags:

- tech
- graphql

---

## GraphQL协议

1. schema即协议，通俗的来讲，可以类似rest openapi schema
2. 客户端以及服务端根据schema通信，根据其定义进行校验约束
3. 客户端在schema范围内可以自由获取（query）或者更新（mutation）需要的字段

```graphql
type Hero {
    id:ID!
    name :String
    friends:[People]
}

type Query{
    heroes:[Hero]
    heroById(id:ID):Hero
}

heroes{
name 
}


data{
heroes: [{name:"A"},{name:"B"}]
}

type Mutation{
updateHero(new:Hero!):Hero
}

updateHero{
id:1
name:"C"
}

data{
updateHero: {id:1 , name:"C"}
}


```

## 需要关注的GraphQL Java概念

- DataFetcher 定义具体数据获取方式，可以是db，memory
- DataLoader 用于 1对n，数据关联查询，解决 n+1 查询问题，用key做了一些cache

## 对应SpringBoot以及Netflix DGS 封装映射

Fetcher

- @Controller @MutationMapping @QueryMapping
- @DgsComponent @DgsMutation @DgsQuery

Loader

- @SchemaMapping @BatchMapping
- @DgsData

## 原生GraphQL SDK demo

- [DataFetcher 定义](https://github.com/graphql-java/graphql-java-examples/blob/master/http-example/src/main/java/com/graphql/java/http/example/GraphQLProvider.java)

```java
    private RuntimeWiring buildWiring(){
        return RuntimeWiring.newRuntimeWiring()
        .type(newTypeWiring("Query")
        .dataFetcher("hero",starWarsWiring.heroDataFetcher)
        .dataFetcher("human",starWarsWiring.humanDataFetcher)
        .dataFetcher("droid",starWarsWiring.droidDataFetcher)
        )
        .type(newTypeWiring("Human")
        .dataFetcher("friends",starWarsWiring.friendsDataFetcher)
        )
        .type(newTypeWiring("Droid")
        .dataFetcher("friends",starWarsWiring.friendsDataFetcher)
        )

        .type(newTypeWiring("Character")
        .typeResolver(starWarsWiring.characterTypeResolver)
        )
        .type(newTypeWiring("Episode")
        .enumValues(starWarsWiring.episodeResolver)
        )
        .build();
        }
```

- [DataFetcher & DataLoader 实现](https://github.com/graphql-java/graphql-java-examples/blob/master/http-example/src/main/java/com/graphql/java/http/example/StarWarsWiring.java)

```java

@Component
public class StarWarsWiring {

    private final DataLoaderRegistry dataLoaderRegistry;

    public StarWarsWiring() {
        this.dataLoaderRegistry = new DataLoaderRegistry();
        dataLoaderRegistry.register("characters", newCharacterDataLoader());
    }

    @Bean
    public DataLoaderRegistry getDataLoaderRegistry() {
        return dataLoaderRegistry;
    }


    private List<Object> getCharacterDataViaBatchHTTPApi(List<String> keys) {
        return keys.stream().map(StarWarsData::getCharacterData).collect(Collectors.toList());
    }

    // a batch loader function that will be called with N or more keys for batch loading
    private BatchLoader<String, Object> characterBatchLoader = keys -> {

        //
        // we are using multi threading here.  Imagine if getCharacterDataViaBatchHTTPApi was
        // actually a HTTP call - its not here - but it could be done asynchronously as
        // a batch API call say
        //
        //
        // direct return of values
        //CompletableFuture.completedFuture(getCharacterDataViaBatchHTTPApi(keys))
        //
        // or
        //
        // async supply of values
        return CompletableFuture.supplyAsync(() -> getCharacterDataViaBatchHTTPApi(keys));
    };

    // a data loader for characters that points to the character batch loader
    private DataLoader<String, Object> newCharacterDataLoader() {
        return new DataLoader<>(characterBatchLoader);
    }

    // we define the normal StarWars data fetchers so we can point them at our data loader
    DataFetcher humanDataFetcher = environment -> {
        String id = environment.getArgument("id");
        Context ctx = environment.getContext();
        return ctx.getCharacterDataLoader().load(id);
    };


    DataFetcher droidDataFetcher = environment -> {
        String id = environment.getArgument("id");
        Context ctx = environment.getContext();
        return ctx.getCharacterDataLoader().load(id);
    };

    DataFetcher heroDataFetcher = environment -> {
        Context ctx = environment.getContext();
        return ctx.getCharacterDataLoader().load("2001"); // R2D2
    };

    DataFetcher friendsDataFetcher = environment -> {
        FilmCharacter character = environment.getSource();
        List<String> friendIds = character.getFriends();
        Context ctx = environment.getContext();
        return ctx.getCharacterDataLoader().loadMany(friendIds);
    };
}
```

## Reference

- https://github.com/eugenp/tutorials/tree/master/spring-boot-modules/spring-boot-graphql
- https://www.baeldung.com/spring-graphql
- https://www.baeldung.com/spring-graphql-error-handling
- https://github.com/danvega/graphql-paging
- https://github.com/danvega/graphql-store
- https://github.com/graphql-java/tutorials
- https://tech.meituan.com/2021/05/06/bff-graphql.html
- https://netflix.github.io/dgs/getting-started/*
