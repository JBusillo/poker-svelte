<script>
  import { onMount } from "svelte";
  import { registerDump } from "../support/Dumper.js";
  import {
    selectedCards,
    selectEnabled,
    myActions
  } from "../support/Communication";

  export let me;

  let selected = [];

  // Bind to store
  onMount(() => {
    selectedCards.reset();
    selectEnabled.set(true);

    return registerDump(
      "SelectCards.svelte",
      me,
      selected,
      $selectEnabled,
      $selectedCards
    );
  });

  // Enable Selection in PlayerCards and TableCards
  // Test to ensure that 5 cards have been selected
  // actions are "ok" and "fold"
  function select(event) {
    let action = event.currentTarget.getAttribute("action");

    if (action === "ok") {
      let counts = selectedCards.getCounts();
      switch (me.rule) {
        case "5Total":
          if (counts.MyCards + counts.TableCards !== 5) return;
          break;
        case "2M/3T":
          if (counts.MyCards !== 2 || counts.TableCards !== 3) return;
          break;
      }
    }

    selectEnabled.set(false);
    me.cb({ action, cards: $selectedCards });
    myActions.set({ type: "MyActions", miniDialog: "default" });
  }
</script>

<style>
  .btn {
    background-color: cadetblue;
    height: 50px;
    width: 100px;
    border-radius: 5px;
    border: 2px solid black;
    font-weight: bold;
    padding: 0px;
  }

  .flexCol {
    display: flex;
    flex-direction: column;
  }

  .flexRow {
    display: flex;
    flex-direction: row;
  }
</style>

<div class="flexCol">
  <div class="flexRow">
    <button id="sc-ok" class="btn" action="ok" on:click={select}>OK</button>
    <button class="btn" action="fold" on:click={select}>Fold</button>
  </div>
  <div>{me.prompt}</div>
</div>
