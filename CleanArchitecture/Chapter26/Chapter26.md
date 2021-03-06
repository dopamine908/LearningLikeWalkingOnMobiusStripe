# Chapter 26 - 主元件

Main元件就是最終的細節－也就是最低層次的策略，你可以想像Main是所有元件中最髒的元件。他是系統最初始的進入點，沒有東西依賴他，他的工作是建立工廠(Factory)、實行策略(Strategy)等等。<br/>

在Main中，依賴關係應該由依賴注入(Dependency Injection)去實施。<br/>

Main將收到的命令需要對應的處理、需要實踐的內容交由較高層級的元件去處理。


```java

public class Main implements HtwMessageReceiver {
  private static HuntTheWumpus game;
  private static int hitPoints = 10;

  public static void main(String[] args) throws IOException {
    //Factory
    game = HtwFactory.makeGame("htw.game.HuntTheWumpusGame", new Main());
    createMap();

    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    game.makeRestCommand().execute();
    
    while (true) {
      System.out.println(game.getPlayerCavern());
      System.out.println("Health: " + hitPoints + " arrows: " + game.getQuiver());
      HuntTheWumpus.Command c = game.makeRestCommand();
      System.out.println(">");
      String command = br.readLine();
      if (command.equalsIgnoreCase("e"))
        c = game.makeMoveCommand(EAST);
      else if (command.equalsIgnoreCase("w"))
        c = game.makeMoveCommand(WEST);
      else if (command.equalsIgnoreCase("q"))
        return;

      c.execute();
    }
  }

  private static void createMap() {
    //do something about create map
  }

}

```