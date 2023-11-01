<script>

const numInputs = document.querySelectorAll("input");
const Select = document.querySelectorAll("select");

let number_of_builds_per_week = 100;
let avg_build_time = 15;
let selected_provider = "circleCI";
let selected_machine_type = "Linux_4_cores";

let machine_cost_per_mins;
let weekly_build_minutes;

let harness_cost;
let circleCI_cost;
let github_actions_cost;
let other_vendor_cost;

let harness_cost_per_mins;
let circleCI_cost_per_mins;
let github_actions_cost_per_mins;
let other_vendor_cost_per_mins;

let harness_saving_percentage;

let annual_cost_with_other_provider = [];
let annual_cost_with_harness = [];
let annual_hours_saved = [];

let annual_hour = 0;
let saved_hour = 0;

document.addEventListener("DOMContentLoaded", function () {
  main();
});

numInputs.forEach(function (input) {
  input.addEventListener("input", function (e) {
    if (e.target.value == "") {
      e.target.value = 0;
    }
    main();
  });
});
Select.forEach(function (select) {
  select.addEventListener("input", function (e) {
    main();
  });
});

function main() {
  const provider = document.getElementById("provider");
  selected_provider = provider.options[provider.selectedIndex].value;
  if (!selected_provider || selected_provider === "") {
    selected_provider = "circleCI";
  }
  const machine_type = document.getElementById("machine_type");
  selected_machine_type =
    machine_type.options[machine_type.selectedIndex].value;
  if (!selected_machine_type || selected_machine_type === "") {
    selected_machine_type = "Linux_4_cores";
  }

  number_of_builds_per_week = document.getElementById("weekly_build").value;
  avg_build_time = document.getElementById("weekly_build_minutes").value;

  calculate(
    selected_provider,
    selected_machine_type,
    avg_build_time,
    number_of_builds_per_week
  );
}

function calculate(
  selected_provider,
  selected_machine_type,
  avg_build_time,
  number_of_builds_per_week
) {
  switch (selected_machine_type) {
    case "Linux_4_cores":
      harness_cost_per_mins = 0.01;
      circleCI_cost_per_mins = 0.012;
      github_actions_cost_per_mins = 0.016;
      other_vendor_cost_per_mins = 0.016;
      harness_saving_percentage = 0.3;

      break;

    case "Linux_8_cores":
      harness_cost_per_mins = 0.025;
      circleCI_cost_per_mins = 0.06;
      github_actions_cost_per_mins = 0.032;
      other_vendor_cost_per_mins = 0.032;
      harness_saving_percentage = 0.3;

      break;
    case "Linux_16_cores":
      harness_cost_per_mins = 0.05;
      circleCI_cost_per_mins = 0.012;
      github_actions_cost_per_mins = 0.064;
      other_vendor_cost_per_mins = 0.064;
      harness_saving_percentage = 0.3;

      break;
    case "Linux_32_cores":
      harness_cost_per_mins = 0.1;
      circleCI_cost_per_mins = 0.18;
      github_actions_cost_per_mins = 0.128;
      other_vendor_cost_per_mins = 0.128;
      harness_saving_percentage = 0.3;

      break;
    case "Windows_4_cores":
      harness_cost_per_mins = 0.04;
      circleCI_cost_per_mins = 0.072;
      github_actions_cost_per_mins = 0.064;
      other_vendor_cost_per_mins = 0.064;
      harness_saving_percentage = 0.3;

      break;
    case "macOS_M1_6_cores":
      harness_cost_per_mins = 0.3;
      circleCI_cost_per_mins = 0.09;
      github_actions_cost_per_mins = 0.08;
      other_vendor_cost_per_mins = 0.08;
      harness_saving_percentage = 0.2;

      break;

    default:
      break;
  }

  weekly_build_minutes = number_of_builds_per_week * avg_build_time * 52;
  harness_cost = Math.round(weekly_build_minutes * harness_cost_per_mins);
  switch (selected_provider) {
    case "circleCI":
        circleCI_cost = Math.round(weekly_build_minutes * circleCI_cost_per_mins);
        break;
    case "github_actions":
        github_actions_cost = Math.round(weekly_build_minutes * github_actions_cost_per_mins);
        break;
    case "other_vendor":
        other_vendor_cost = Math.round(weekly_build_minutes * other_vendor_cost_per_mins);
        break;
    default:
        break;
        
  }
  const other_provider = document.getElementById("other_provider");
  const harness_provider = document.getElementById("harness");
  const hour_saved = document.getElementById("hour_saved");
  let table = document.querySelector("table");

  if (!table) {
    annual_hour = (number_of_builds_per_week * avg_build_time * 52) / 60;
    saved_hour = 0.3 * annual_hour;
    const hours = hour_saved.getElementsByTagName("h2")[0];
    hours.textContent = `${Math.round(saved_hour)} `;
    console.log(hours.textContent);
    if (other_provider) {
      const provider = other_provider.getElementsByTagName("h3")[0];
      const cost = other_provider.getElementsByTagName("h2")[0];
      if (selected_provider === "circleCI") {
        provider.textContent = "Annual cost with CircleCI";
        cost.textContent = `$ ${circleCI_cost} `;
      } else {
        provider.textContent = "Annual cost with Git Actions";
        cost.textContent = `$ ${github_actions_cost} `;
      } else {
        provider.textContent = "Annual cost with Other Vendor";
        cost.textContent = `$ ${other_vendor_cost} `;
      }
    }

    if (harness_provider) {
      const cost = harness_provider.getElementsByTagName("h2")[0];
      cost.textContent = ` $ ${harness_cost} `;
    }
  } else {
    const tbody = table.getElementsByTagName("tbody");

    const hours = hour_saved.getElementsByTagName("h2")[0];

    annual_hour = (number_of_builds_per_week * avg_build_time * 52) / 60;
    saved_hour = 0.3 * annual_hour;
    const sum = annual_hours_saved.reduce((partialSum, a) => partialSum + a, 0);
    hours.textContent = `${Math.round(sum + saved_hour)} `;

    if (other_provider) {
      const provider = other_provider.getElementsByTagName("h3")[0];
      const cost = other_provider.getElementsByTagName("h2")[0];

      const sum = annual_cost_with_other_provider.reduce(
        (partialSum, a) => partialSum + a,
        0
      );
      if (selected_provider === "circleCI") {
        provider.textContent = "Annual cost with CircleCI";
        cost.textContent = `$ ${circleCI_cost + sum} `;
      } else {
        provider.textContent = "Annual cost with Git Actions";
        cost.textContent = `$ ${github_actions_cost + sum} `;
      } else {
        provider.textContent = "Annual cost with Other Vendor";
        cost.textContent = `$ ${other_vendor_cost + sum} `;
      }
    }
    if (harness_provider) {
      const cost = harness_provider.getElementsByTagName("h2")[0];

      const sum = annual_cost_with_harness.reduce(
        (partialSum, a) => partialSum + a,
        0
      );
      cost.textContent = ` $ ${harness_cost + sum} `;
    }
  }
}

function addToArray() {
  const saved_hours = 0.3 * (weekly_build_minutes / 60);
  annual_hours_saved.push(saved_hours);

  annual_cost_with_harness.push(harness_cost);
  circleCI_cost
    ? annual_cost_with_other_provider.push(circleCI_cost)
    : annual_cost_with_other_provider.push(github_actions_cost);
    : annual_cost_with_other_provider.push(other_vendor);

  console.log(annual_hours_saved);

  main();
}
function removeFromArray(other, harness, annual_hrs_saved) {
  removeFirst = function (val, array) {
    array.splice(array.indexOf(val), 1);
    return array;
  };

  removeFirst(other, annual_cost_with_other_provider);
  removeFirst(harness, annual_cost_with_harness);
  removeFirst(annual_hrs_saved, annual_hours_saved);

  main();
}

add_additional.addEventListener("click", addToArray);
</script> 





# saving.calc.js
