Snake
   bitePosition
   tailPosition

Dice
   int roll();

Cell
   int cellId;
   List<Player> getPlayersOnCell();
   booelan isGameFinishCell();
   

Player
   Cell location;
   Cell getCurrentLocation();
   void	setCurrentLocation(Cell cell);	

Board
   private Map<Cell bitePosition, Cell tailPosition> snakes;
   private Map<Player, Cell> playerLocationMap;	   

   boolean willSnakeBiteOnCell(Cell currentCellid);
   Cell	   newCellOnSnakeBiteOnCell(Cell currentCellId);	
   Cell	   getPlayerCurrentLocation(Player player);	   	

Game
     //to manage turns	
     Queue<Player> playerQueue;	

      Game(List<Player> playerList, Board board, Dice dice);
	
      // each player plays turn by turn	
      Player play(){
		 
		while(1) {
			// Player currentPlayer = remove top player of queue
			// rotate dice to get offset
			// find new location of player via offset
			// if Board.willSnakeBiteOnCell(Cell newLocation) -> update new Location
			// Player.setLocation(newLocation)
			// check player has reached final cell
			// if cell.isGameFinishCell() -> return currentPlayer as winner
			// otherwise, add currentPlayer to endOf queue
			
		}
      }

      Status getStatus();
	     setStatus(Status status);
      Player getPlayerWithNextTurn();
      void   setPlayerWithNextTurn(Player player);
	      	     			
