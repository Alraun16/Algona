<%*
const eventTypes = [
  "Обычная дата",
  "Поселение / город / государство",
  "Война / восстание / конфликт"
];

const eventClasses = [
  "",
  "settlement",
  "war"
];

const dateModes = [
  "Старая эпоха / С.э.",
  "Новая эпоха / Н.э. + авто С.э.",
  "Произвольная дата"
];

const dateModeValues = [
  "se",
  "ne",
  "custom"
];

const eventType = await tp.system.suggester(eventTypes, eventClasses);
const dateMode = await tp.system.suggester(dateModes, dateModeValues);

let dateText = "";
let yearText = "";
let isPeriod = false;

function normalizeRange(value) {
  return value
    .trim()
    .replace(/\s*-\s*/g, "–")
    .replace(/\s*—\s*/g, "–");
}

function hasRange(value) {
  return value.includes("–");
}

function yearSuffix(value) {
  return hasRange(value) ? "гг." : "г.";
}

function addToYear(value, offset) {
  const normalized = normalizeRange(value);

  if (hasRange(normalized)) {
    const parts = normalized.split("–").map(part => part.trim());
    const start = Number(parts[0]);
    const end = Number(parts[1]);

    if (Number.isNaN(start) || Number.isNaN(end)) {
      return normalized;
    }

    return `${start + offset}–${end + offset}`;
  }

  const year = Number(normalized);

  if (Number.isNaN(year)) {
    return normalized;
  }

  return `${year + offset}`;
}

if (dateMode === "se") {
  yearText = normalizeRange(await tp.system.prompt("Год или диапазон С.э. Например: 5742 или 2647–2689"));
  isPeriod = hasRange(yearText);
  dateText = `<span class="year">${yearText}</span> ${yearSuffix(yearText)} С.э.`;
}

if (dateMode === "ne") {
  const yearNe = normalizeRange(await tp.system.prompt("Год или диапазон Н.э. Например: 3903 или 1–70"));
  const yearSe = addToYear(yearNe, 5742);

  isPeriod = hasRange(yearNe);
  dateText = `<span class="year">${yearNe}</span> ${yearSuffix(yearNe)} Н.э. / <span class="year">${yearSe}</span> ${yearSuffix(yearSe)} С.э.`;
}

if (dateMode === "custom") {
  const rawDate = await tp.system.prompt("Полная дата как должна отображаться. Например: ~500 тыс. лет до С.э.");
  const rawYear = await tp.system.prompt("Что подчёркивать как год/диапазон? Например: ~500 тыс. или 0–1800");

  isPeriod = hasRange(rawYear);
  dateText = rawDate.replace(rawYear, `<span class="year">${rawYear}</span>`);
}

const description = await tp.system.prompt("Описание события");

const classes = [];

if (isPeriod) {
  classes.push("period");
}

if (eventType) {
  classes.push(eventType);
}

const classText = classes.length > 0 ? ` class="${classes.join(" ")}"` : "";

tR += `<li${classText}><span><span class="date">${dateText}</span> — ${description}</span></li>`;
%>