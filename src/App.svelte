<script lang="ts">
  const name: string = "world";

  const num_rows = 10;
  const num_columns = 4;

  let player1Turn = true;
  let score = { player1: 0, player2: 0 };
  type Edge = { type: "edge"; clicked: boolean };
  type Point = { type: "point" };
  type Space = { type: "space"; owned: SpaceOwned };
  type Cell = Edge | Point | Space;

  type SpaceOwned = "player1" | "player2" | "nobody";

  type Board = Cell[][];
  let board: Board = [];
  for (let x = 0; x < num_columns * 2 + 1; x++) {
    board.push([]);
    for (let y = 0; y < num_rows * 2 + 1; y++) {
      if (x % 2 === 1 && y % 2 === 1) {
        board[x].push({ type: "space", owned: "nobody" });
      } else if (x % 2 === 0 && y % 2 === 0) {
        board[x].push({ type: "point" });
      } else {
        board[x].push({ type: "edge", clicked: false });
      }
    }
  }

  function handleClick(edge: Edge) {
    if (!edge.clicked) {
      edge.clicked = true;
      const fillResult = fillSpaces(board);
      board = fillResult.board;
      if (!fillResult.spaceFilled) {
        player1Turn = !player1Turn;
      }
    }
  }

  function isCellClickedEdge(cell: Cell): boolean {
    if (cell.type === "edge") {
      return cell.clicked;
    } else {
      throw Error("only edges are expected here");
    }
  }

  function fillSpaces(board: Board): { board: Board; spaceFilled: boolean } {
    let spaceFilled = false;
    for (const [y, row] of board.entries()) {
      for (const [x, cell] of row.entries()) {
        if (cell.type === "space" && cell.owned === "nobody") {
          if (
            isCellClickedEdge(board[y][x - 1]) &&
            isCellClickedEdge(board[y][x + 1]) &&
            isCellClickedEdge(board[y - 1][x]) &&
            isCellClickedEdge(board[y + 1][x])
          ) {
            cell.owned = player1Turn ? "player1" : "player2";
            score[cell.owned] += 1;
            spaceFilled = true;
          }
        }
      }
    }
    return { board, spaceFilled };
  }
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: mediumturquoise;
    text-transform: uppercase;
    font-size: 5em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <h1>Hello little {name}!</h1>
  <h2>{player1Turn ? 'Player 1' : 'Player 2'}</h2>
  <h3>Score: {score.player1}:{score.player2}</h3>
  <table>
    {#each board as row}
      <tr>
        {#each row as cell}
          {#if cell.type === 'edge'}
            <td>
              <button
                on:click={() => handleClick(cell)}>{cell.clicked ? 'clicked!' : 'edge'}</button>
            </td>
          {:else if cell.type === 'point'}
            <td>{cell.type}</td>
          {:else}
            <td>{cell.owned}</td>
          {/if}
        {/each}
      </tr>
    {/each}
  </table>
</main>
