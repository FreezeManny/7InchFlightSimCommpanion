<script lang="ts">
  import { settings } from "$lib/settings";
  import { EventEmitter } from 'events';

  const emitter = new EventEmitter();


  const VATSIMDATAURL = "https://data.vatsim.net/v3/vatsim-data.json";
  const VATSIM_METAR_URL = "https://metar.vatsim.net/";

  var depAirportConf = "Departure Airport: XXXX";
  var arrAirportConf = "Departure Airport: XXXX";

  var depAirport = "";
  var arrAirport = "";

  var depAtisCode = "";
  var arrAtisCode = "";

  var depATIS = "Enter an Airport";
  var arrATIS = "Enter an Airport";

  var depMETAR = "Enter an Airport";
  var arrMETAR = "Enter an Airport";

  enum fetchMode {
    DEP,
    ARR,
  }



  async function fetchSimbriefRoute(){
    console.log("");

    let data:any;
    try {
      const response = await fetch(
        "https://www.simbrief.com/api/xml.fetcher.php?username=" +
          settings.simbriefUsername +
          "&json=1",
      );
      data = await response.json();
      //console.log(data);
      depAirport = data.origin.icao_code;
      arrAirport = data.destination.icao_code;
    } catch (error) {
      console.error("Failed to fetch data:", error);
    }
  }

  async function fetchMETAR(mode: fetchMode) {
    try {
      console.log("fetchMetar")
    var tmpAirport = "";
      var tmpATIS = "";
      var tmpAtisCode = "";
      //console.log(data);
      var atisList: any = [];

      if (mode == fetchMode.DEP) tmpAirport = depAirport.toUpperCase();
      else if (mode == fetchMode.ARR) tmpAirport = arrAirport.toUpperCase();
      const response = await fetch(VATSIM_METAR_URL + tmpAirport);
      if (!response.ok) {
        throw new Error("Network response was not ok");
      }
      const data = await response.text();
        console.log(data);
        if (mode == fetchMode.DEP) depMETAR = data;
      else if (mode == fetchMode.ARR) arrMETAR = data;

    } catch (error) {
      console.error("There was a problem fetching the data:", error);
    }
  }

  async function fetchATIS(mode: fetchMode) {
    try {
      const response = await fetch(VATSIMDATAURL);
      if (!response.ok) {
        throw new Error("Network response was not ok");
      }
      const data = await response.json();
      const atisData = data.atis;

      var tmpAirport = "";
      var tmpATIS = "";
      var tmpAtisCode = "";
      //console.log(data);
      var atisList: any = [];

      if (mode == fetchMode.DEP) tmpAirport = depAirport.toUpperCase();
      else if (mode == fetchMode.ARR) tmpAirport = arrAirport.toUpperCase();

      atisData.forEach((element: any) => {
        if (element.callsign.includes(tmpAirport)) {
          console.log(element);
          atisList.push(element);
        }
      });
      console.log(atisList.length);

      if (atisList.length == 1) {
        tmpAtisCode = atisList[0].atis_code;
        tmpATIS = atisList[0].text_atis;
      } else if (atisList.length == 2) {
        tmpATIS += atisList[0].text_atis;
        tmpATIS += "<br><br>";
        tmpATIS += atisList[1].text_atis;
      } else {
        tmpATIS = "No ATIS Online";
      }

      
      if (mode == fetchMode.DEP) {
        depATIS = tmpATIS;
        depAtisCode = tmpAtisCode;
      }
      if (mode == fetchMode.ARR) {
        arrATIS = tmpATIS;
        arrAtisCode = tmpAtisCode;
      }

    } catch (error) {
      console.error("There was a problem fetching the data:", error);
    }
  }

  function depInputHandler() {
    depAirport = depAirport.toUpperCase();

    if (depAirport.length == 4) {
      depAirportConf = "Departure Airport: " + depAirport;
      fetchATIS(fetchMode.DEP);
      fetchMETAR(fetchMode.DEP);
    } else if (depAirport.length > 4) {
      depAirport = depAirport.slice(0, 4);
    }
  }

  function arrInputHandler() {
  arrAirport = arrAirport.toUpperCase();

  if (arrAirport.length == 4) {
    arrAirportConf = "Departure Airport: " + arrAirport;
    fetchATIS(fetchMode.ARR);
    fetchMETAR(fetchMode.ARR);
  } else if (arrAirport.length > 4) {
    arrAirport = arrAirport.slice(0, 4);
  }
}

async function simbriefButtonHandler() {
  console.log("Simbrief Button");
  await fetchSimbriefRoute();
  depAirportConf = "Departure Airport: " + depAirport;
  arrAirportConf = "Arrival Airport: " + arrAirport;
  fetchATIS(fetchMode.DEP);
  fetchATIS(fetchMode.ARR);
  fetchMETAR(fetchMode.DEP);
  fetchMETAR(fetchMode.ARR);
}

</script>

<div class="grid grid-cols-3 p-2">
  <div class="input-group input-group-divider grid-cols-[auto_1fr_auto]">
    <div class="input-group-shim">Dep</div>
    <input
      type="text"
      placeholder="EDDS"
      bind:value={depAirport}
      on:input={depInputHandler}
    />
  </div>

  <div class="input-group input-group-divider grid-cols-[auto_1fr_auto]">
    <div class="input-group-shim">Arr</div>
    <input type="text" placeholder="EDDS" bind:value={arrAirport} on:input={arrInputHandler}/>
  </div>

  <button type="button" class="btn variant-filled" on:click={simbriefButtonHandler}>Simbrief</button>
</div>

<!--<div class="overflow-scroll overflow-y-auto  h-96 p-3">-->
<div class="h-full max-h-screen overflow-y-auto">
  <div class="grid grid-rows-2 grid-flow-col gap-4">
    <div class="card">
      <header class="card-header">{depAirportConf}</header>
      <section class="p-4">
        <div class="card">
          <header class="card-header">ATIS {depAtisCode}</header>
          <section class="p-4">{@html depATIS}</section>
        </div>
        <div class="card">
          <header class="card-header">METAR</header>
          <section class="p-4">{depMETAR}</section>
        </div>
      </section>
    </div>

    <div class="card">
      <header class="card-header">{arrAirportConf}</header>
      <section class="p-4">
        <div class="card">
          <header class="card-header">ATIS {arrAtisCode}</header>
          <section class="p-4">{arrATIS}</section>
        </div>
        <div class="card">
          <header class="card-header">METAR</header>
          <section class="p-4">{arrMETAR}</section>
        </div>
      </section>
    </div>
    <!--
  <div class="card">
    <header class="card-header">(header)</header>
    <section class="p-4">(content)</section>
  </div>
  -->
  </div>
</div>
