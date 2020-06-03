---
layout: post
title: java switch case 太多怎么重构
date: 2020-06-03 20:30
author: admin
comments: true
categories: [java]
tags: [refactor, solid]
---

> Single-responsibility Principle.单一职责  
Open-closed Principle.开闭原则  
Liskov substitution principle.里氏替换原则  
Interface segregation principle.接口隔离原则  
Dependency Inversion principle.依赖倒转原则  

**结论：对于switch的case then, 要么用map绑定Map<case,then>，要么让case自己实现then**  

switch语句开发遇到多分支场景的时候经常用到，然而switch语句是不符合SOLID原则的(单一职责/开闭原则)，如果switch语句里面case太多怎么重构  

1. 通过enum绑定各有特色的实现方法，来实现策略设计模式
``` java
public enum PlayerTypes { 
    TENNIS,FOOTBALL, SNOOKER
}

public class PlayerCreator {
   public Player createPlayer(PlayerTypes playerType)
         { switch (playerType) {
      case TENNIS:
         return new TennisPlayer();
      case FOOTBALL:
         return new FootballPlayer();
      case SNOOKER:
         return new SnookerPlayer();

      default:
         throw new IllegalArgumentException("Invalid player type: "
            + playerType);
      }
   }
}
```
在enum里定义一个抽象，不同Constant实现方法不同

``` java
public enum PlayerTypes { 
    TENNIS {
        @Override
        public Player createPlayer() {
            return new TennisPlayer();
        }
    },
    FOOTBALL {
        @Override
        public Player createPlayer() {
            return new FootballPlayer();
        }
    },
    SNOOKER {
        @Override
        public Player createPlayer() {
            return new SnookerPlayer();
        }
    };

    public abstract Player createPlayer();
}

// 这样获取Player对象就不需要switch语句了:

Player footballPlayer = PlayerTypes.valueOf("FOOTBALL").createPlayer();
```

2. 不同于方法1，通过第三方Map来保存enum到实现的映射关系，来实现命令设计模式

``` java
// First, we define an interface:

public interface Command {

   Player create();
}

// Second, we provide implementations of this interface for each player type:

public class CreatePlayerCommand {

   private static final Map<String, Command> PLAYERS;

   static {
      final Map<String, Command> players = new HashMap<>();
      players.put("TENNIS", new Command() {
         @Override
         public Player create() {
            return new TennisPlayer();
         }
      });

      players.put("FOOTBALL", new Command() {
         @Override
         public Player create() {
            return new FootballPlayer();
         }
      });

      players.put("SNOOKER", new Command() {
         @Override
         public Player create() {
            return new SnookerPlayer();
         }
      });

      PLAYERS = Collections.unmodifiableMap(players);
   }

   public Player createPlayer(String playerType) {
      Command command = PLAYERS.get(playerType);

      if (command == null) {
         throw new IllegalArgumentException("Invalid player type: "
            + playerType);
      }

      return command.create();
   }

}

// 这样获取Player对象同样不需要switch语句了:

CreatePlayerCommand createCommand = new CreatePlayerCommand();
Player snookerPlayer = createCommand.createPlayer("SNOOKER");
```

3. 跟方法2类似，不过Map的值不再是命令，而是java8的Supplier  
``` java
public class PlayerSupplier {
   private static final Map<String, Supplier<Player>>
      PLAYER_SUPPLIER;

   static {
      final Map<String, Supplier<Player>> players = new HashMap<>();
      players.put("TENNIS", TennisPlayer::new);
      players.put("FOOTBALL", FootballPlayer::new);
      players.put("SNOOKER", SnookerPlayer::new);
      // 不可变视图
      PLAYER_SUPPLIER = Collections.unmodifiableMap(players);
   }

   public Player supplyPlayer(String playerType) {
      Supplier<Player> player = PLAYER_SUPPLIER.get(playerType);

      if (player == null) {
         throw new IllegalArgumentException("Invalid player type: "
            + playerType);
      }
      return player.get();
   }
}
// 这样获取Player对象同样不需要switch语句

PlayerSupplier playerSupplier = new PlayerSupplier();
Player snookerPlayer = playerSupplier.supplyPlayer("SNOOKER");
```

4. 除了返回Player，如果想返回带参数的函数怎么办  
``` java
@FunctionalInterface
public interface TriFunction<T, U, V, R> {
   R apply(T t, U u, V v);
}

public final class FunctionalStatistics {

   // 关键点在于定义一个FunctionMap
   private static final Map<String, TriFunction<TennisPlayer, Period, String, String>> STATISTICS = new HashMap<>();

   static {
      STATISTICS.put("SERVE", Statistics::computeServeTrend);
      STATISTICS.put("FOREHAND", Statistics::computeForehandTrend);
      STATISTICS.put("BACKHAND", Statistics::computeBackhandTrend);
   }

   public static String computeTrend(TennisPlayer tennisPlayer,
      Period period, String owner, String trend) {
      TriFunction<TennisPlayer, Period, String, String>
            function = STATISTICS.get(trend);

      if (function == null) {
         throw new IllegalArgumentException("Invalid trend type: "
            + trend);
         }

      return function.apply(tennisPlayer, period, owner);
   }
}
// Getting the FOREHAND trend (we use dummy arguments, because they are not relevant):

String forehandTrend = FunctionalStatistics.computeTrend(
    new TennisPlayer(), Period.ZERO, "SPORT TV", "FOREHAND");
```

5. 把switch语句挪到工厂里， 抽象工厂设计模式  
``` java
public interface AbstractPlayerFactory {

   public Player createPlayer(Type type, int delta);
}

public class PlayerFactory implements AbstractPlayerFactory {

   @Override
   public Player createPlayer(Type type, int delta) {
      switch (type) {
         case TENNIS:
            return new TennisPlayer(type, delta);
         case FOOTBALL:
            return new FootballPlayer(type, delta);
         case SNOOKER:
            return new SnookerPlayer(type, delta);

         default:
         throw new IllegalArgumentException("Invalid player type: "
            + type);
      }
   }

}
```

6. 如果switch语句里面还嵌套着判断语句，则可以用Predicate来实现  
``` java
public enum PlayerTypes {

   TENNIS(Collections.unmodifiableList(Arrays.asList(
      () -> new TennisPlayer("Rafael Nadal"),
      () -> new TennisPlayer("Roger Federer"),
      () -> new TennisPlayer("Andy Murray"))
   ),
   Collections.unmodifiableList(Arrays.asList(
      rank -> rank == 1, rank -> rank > 1 &&
      rank < 5, rank -> rank >= 5 && rank <= 10))
   ),
   FOOTBALL(Collections.unmodifiableList(Arrays.asList(
      () -> new FootballPlayer("Lionel Messi"),
      () -> new FootballPlayer("Cristiano Ronaldo"))
   ),
   Collections.unmodifiableList(Arrays.asList(
      rank -> rank == 1 || rank == 2,
      rank -> rank > 2 && rank <= 10))
   ),
   SNOOKER(Collections.unmodifiableList(Arrays.asList(
      () -> new SnookerPlayer("Ronnie O'Sullivan"),
      () -> new SnookerPlayer("Mark Selby"),
      () -> new SnookerPlayer("John Higgins"),
      () -> new SnookerPlayer("Neil Robertson"))
   ),
   Collections.unmodifiableList(Arrays.asList(
      rank -> rank == 1, rank -> rank == 2,
      rank -> rank > 3 && rank < 7,
      rank -> rank >= 7 && rank <= 10))
   );

   private final List<Supplier<Player>> names;
   private final List<Predicate<Integer>> conditions;

   // 2个 有序列表，一个保存Player，一个保存判断条件
   private PlayerTypes(List<Supplier<Player>> names,
         List<Predicate<Integer>> conditions) {
      this.names = names;
      this.conditions = conditions;
   }

   public static final Player supplyPlayer(String playerType,
         int rank) {

      if (rank < 1 || rank > 10) {
         throw new IllegalArgumentException("Invalid rank: " +
            rank);
      }

      List<Predicate<Integer>> selectors =
         PlayerTypes.valueOf(playerType).conditions;

      for (int i = 0; i < selectors.size(); i++) {
         if (selectors.get(i).test(rank)) {
            return PlayerTypes.valueOf(playerType)
               .names.get(i).get();
         }
      }

      throw new IllegalStateException("The enum is corrupted");
   }

}
// Obtaining the output, Snooker player: Neil Robertson:

Player snookerPlayer = PlayerTypes.supplyPlayer("SNOOKER", 10);
```
