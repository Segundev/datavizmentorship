<script>
  import "../style.css";

  import nig from "../lib/nigeria.json";
  import data from "../lib/data.json";
  import Scrolly from "../lib/Scrolly.svelte";

  import { geoPath, geoIdentity } from "d3-geo";
  import { interpolateReds } from "d3-scale-chromatic";
  import { scaleLinear } from "d3-scale";

  let currentStep;
  let width = 400;
  let height = 400;

  let states = nig.features;

  function mapLogic(year) {
    let mapData = {};
    data.forEach((d) => {
      mapData[d["State"]] = d[year];
    });

    return mapData;
  }

  $: projection = geoIdentity()
    .reflectY(true)
    .fitExtent(
      [
        [20, 20],
        [width, height],
      ],
      nig
    );

  const colorScale = scaleLinear()
    .range([interpolateReds(0.3), interpolateReds(1)])
    .domain([0, 0.6]);

  $: path = geoPath(projection);

  let value = mapLogic(2023);
  let year;

  $: if (currentStep == 0) {
    (value = mapLogic(2023)), (year = 2023);
  } else if (currentStep == 1) {
    value = mapLogic(2019);
    year = 2019;
  } else if (currentStep == 2) {
    value = mapLogic(2015);
    year = 2015;
  }

  const steps = [
    "<p>In 2023, Voters participation among the south south and south eastern states reduced significantly. Bayelsa state recorded its lowest compared to the previous presidential election of 2015 and 2019</p>",
    "<p>In 2019, the decline right from 2007 was obvious in Enugu, Cross River, Rivers, Akwa Ibom and other neigbouring southern states. Most of this states recorded a turnout above 40% in 2015 but declined to an average below 25% in 2019</p>",
    "<p>Even though voters participation rate was above 40% which implies at most 5 in 10 Nigerians voted, Lagos performed woefully and recorded a turnout below Borno state, a period where the terrorism in the state was at its peak</p>",
  ];
</script>

<div class="wrapper">
  <header>
    <div class="gif">
      <img src="ballotbox.gif" alt="An animation of a bollotbox" />
    </div>
    <div class="title">
      <h1>Using charts and maps, how did Nigerian voters turn out?</h1>
    </div>
    <div class="tagline">
      <p><span class="spantag">By</span> Jayeola Gbenga</p>
    </div>
  </header>
  <section class="main">
    <div class="into">
      <p>
        The number of voters who participate in an election has a significant
        impact on the outcome. Several media outlets projected the outcome of
        the election on the basis of a large turnout. However, only 24.9 million
        voted out of 93.47 million registered voters. In simpler words,
        approximately only 3 out of 10 registered voters were able to cast their
        vote. This compared to 7 out of 10 in 2003.
      </p>
    </div>
    <div class="headline-intro">
      <p>
        The graphics below highlight significant facts concerning trends in
        voter turnout in Nigeria.
      </p>
    </div>
    <section>
      <div class="gif">
        <img
          src="slope.gif"
          alt="animation showing showing consistent decrease in voters turnout"
        />
      </div>
      <p>
        Voter turnout in the 2023 presidential and national assembly elections
        was the lowest ever recorded since democracy was established in 1999.
        The participation rate recorded was 26.74% , implying that barely 3 out
        of every 10 registered voters exercised their voting rights on election
        day by casting their votes. Between 1999 and 2011, voter participation
        on average was greater than 50%, with more than 5 out of 10 registered
        voters eager to cast their votes.
      </p>
      <div class="chart">
        <picture>
          <source srcset="slopechart-desktop.png" media="(min-width: 720px)" />
          <img
            src="slopechart-mobile.png"
            alt="chart showing registered voters compared to total voters"
          />
        </picture>
      </div>
    </section>
    <section>
      <div class="gif">
        <img
          src="barchart.gif"
          alt="animation showing showing consistent decrease in voters turnout"
        />
      </div>
      <p>
        Voter turnout has been declining since 2007. Even though the number of
        registered voters has increased throughout the years, participation rate
        has been declining. The 2023 election surprised many Nigerians, who
        expected a huge voter participation due to the country's economic woes
        prior to the election. In addition, 40% of registered voters were
        between the ages of 18 and 34. These figures were expected to have an
        impact on election day, as witnessed during the Endsars protest in 2020,
        when youths took to the streets to demand the disbandment of the popular
        police unit, the Special Anti-Robbery Squad, owing to incidents of
        extrajudicial executions, extortion, and intimidation.
      </p>
      <div class="chart">
        <picture>
          <source srcset="barchart-desktop.png" media="(min-width: 720px)" />
          <img
            src="barchart-mobile.png"
            alt="chart showing percentage of voters registration over the years"
          />
        </picture>
      </div>
    </section>
    <section>
      <div class="gif">
        <img
          src="map.gif"
          alt="animation showing showing consistent decrease in voters turnout"
        />
      </div>
      <p>
        Southern states have a low level of interest. In the just-concluded
        election, Rivers State recorded the lowest turnout among 36 states,
        including the FCT. All the states in the south-south and south-east
        geopolitical zones were less than the national average of 26.7%.
      </p>
      <div class="section-container">
        <div class="steps-container">
          <Scrolly bind:value={currentStep}>
            {#each steps as text, i}
              <div class="step" class:active={currentStep === i}>
                <div class="step-content">{@html text}</div>
              </div>
            {/each}
            <div class="spacer" />
          </Scrolly>
        </div>
        <div class="sticky">
          <div bind:clientWidth={width}>
            <h4 class="mapheader">
              Map showing voters participation in <span>{year}</span> across Nigeria
              states
            </h4>
            <svg {width} {height}>
              <!-- Countries -->
              {#each states as county}
                <path
                  d={path(county)}
                  fill={colorScale(value[county.properties.admin1Name])}
                  stroke="none"
                />
                <text
                  class="text"
                  dx="-5"
                  x={path.centroid(county)[0] - 10}
                  y={path.centroid(county)[1]}
                >
                  {county.properties.admin1Name}</text
                >
              {/each}
            </svg>
          </div>
        </div>
      </div>
    </section>
    <section>
      <div class="gif">
        <img
          src="scatter.gif"
          alt="animation showing showing consistent decrease in voters turnout"
        />
      </div>
      <p>
        Lagos State has the highest number of registered voters. Likewise, the
        former British colony and nation's capital has the highest and most
        educated populace in the country. This, however, has had little effect
        on voters turnout. Moreso, some of the states with high numbers of
        educated citizens have failed to turn up during the election period.
        Reverse is the case in less educated states. In the last election,
        Jigawa State, with the least educated population in the nation, recorded
        the highest turnout rate.
      </p>
      <div class="chart">
        <picture>
          <source srcset="scatterplot-desktop.png" media="(min-width: 720px)" />
          <img
            src="scatterplot-mobile.png"
            alt="scatterplot showing education literacy rate and voters participation"
          />
        </picture>
      </div>
    </section>
    <section>
      <p>
        Election in Nigeria has always been entangled with violence and fraud.
        The introduction of Card readers in 2015 reduced some of the loopholes.
        But individuals could still by pass the system. In 2021, INEC introduced
        the Bimodal Voter Accreditation System to improve the election
        architecture. PVC authentication and Voter accreditation, electoral
        officers could easily detect and malpractise in place. Insurgency in the
        North and the lingering sit-at-home by IPOB resulted in low voters
        turnout in Northern and South eastern part of Nigeria.
      </p>
    </section>
    <section>
      <p>
        Another factor affecting low voters turnout is the uncertainty in the
        true number of Registered voters. With the high rate of Nigerians
        migrating to the west and also individuals who have passed away in the
        last couple of years, the present number of registered voters does not
        reflect the genuine picture. Again, the data often released by INEC do
        not contain certain level of details like gender, occupation, age and
        education level that should help gain insight to the continuous decline
        in voters participation.
      </p>
    </section>
    <section>
      <p>
        As earlier mentioned in the beginning of the article, voter turnout has
        a significant impact on the outcome of an election. President Bola Ahmed
        Tinubu won the election with 36 percent of total votes, the lowest when
        compared to other elections. Opposing parties are in court trying to
        regain their mandate, but the dynamics would have been different if more
        Nigerians had come out to vote on the 25th of February, 2023.
      </p>
    </section>
    <section class="footnote">
      <p>
        <span> Note:</span> The story serves as a capstone project under the Data
        Visualization Society mentorship program.
      </p>
      <p>
        <span> Data Source:</span> Independent National Electoral Commission, Dataphyte,
        Wikipedia, International IDEA, National Bureau of Statistics.
      </p>
      <p>
        <span> Edited and Mentored by:</span> Can Kayali
      </p>
    </section>
  </section>
</div>

<style>
  :global(body) {
    overflow-x: hidden;
  }

  .mapheader {
    font-weight: 600;
  }

  .mapheader span {
    color: #cc1414;
  }

  .text {
    font-family: "Helvetica";
    font-size: 10px;
    fill: #fdfdfd;
  }

  .spacer {
    height: 40vh;
  }

  .sticky {
    position: sticky;
    margin: auto;
    width: 90%;
    top: 10%;
  }

  .section-container {
    margin-top: 1em;
    text-align: center;
    transition: background 100ms;
    display: flex;
    flex-direction: column-reverse;
  }

  .step {
    height: 80vh;
    display: flex;
    place-items: center;
    justify-content: center;
  }

  .step-content {
    font-size: 1rem;
    background: whitesmoke;
    color: #ccc;
    border-radius: 5px;
    padding: 0.5rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 500ms ease;
    box-shadow: 1px 1px 10px rgba(0, 0, 0, 0.2);
    text-align: left;
    width: 75%;
    margin: auto;
    max-width: 500px;
  }

  .step.active .step-content {
    background: white;
    color: black;
  }

  .steps-container,
  .sticky {
    height: 100%;
  }

  .steps-container {
    flex: 1 1 40%;
    z-index: 10;
  }

  .wrapper {
    max-width: 390px;
    margin: 0 auto;
    text-align: center;
    padding: 20px;
  }

  section,
  section p {
    margin: 2rem 0;
    position: relative;
    font-size: 0.9rem;
  }

  header {
    margin-top: 2rem;
  }
  .gif {
    margin: 0 auto;
    text-align: center;
  }

  .chart {
    position: relative;
    text-align: center;
  }

  .tagline {
    font-family: "Lato";
    font-size: 1rem;
    font-weight: 600;
    margin: 1.5rem 0;
  }

  .spantag {
    font-weight: 300;
    font-style: italic;
    font-family: "Tinos";
  }

  .main {
    text-align: initial;
  }

  .headline-intro {
    font-weight: 600;
    margin: 2.5rem 0;
  }

  .footnote {
    margin-top: 4rem;
    border-top: 1rem;
  }

  .footnote p {
    margin: 0.9rem;
    font-size: 0.85rem;
  }

  .footnote span {
    font-weight: 900;
  }

  @media (width > 560px) {
    .wrapper {
      max-width: 540px;
      margin: 0 auto;
      padding: 10px;
    }

    .chart {
      right: 2rem;
    }

    section p {
      font-size: 1rem;
    }
  }

  @media (width > 860px) {
    .wrapper {
      max-width: 700px;
      margin: 0 auto;
      padding: 10px;
    }

    .chart {
      right: 1rem;
    }
  }
</style>
