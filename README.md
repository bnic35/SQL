# SQL
Practice SQL Code with Microsoft SQL Server
--Looking at all data in Covid Deaths
SELECT *
FROM CovidDeaths;

--Looking at all data in Covid Vaccinations
SELECT *
FROM CovidVacs;

-- Number of rows in Covid Deaths
SELECT DISTINCT COUNT(*)
FROM CovidDeaths;

-- Country with the most Covid Cases Recorded
SELECT location,
	SUM(total_cases_per_million) AS num_cases
FROM CovidDeaths
WHERE continent IS NOT NULL
GROUP BY location
ORDER BY num_cases DESC;

-- Month with most recorded new cases
SELECT DATEPART(MONTH, date) AS month,
	SUM(new_cases) AS total_new_cases
FROM CovidDeaths
WHERE continent IS NOT NULL
GROUP BY DATEPART(MONTH, date)
ORDER BY total_new_cases DESC;

-- Top 10 Countries with the highest population density
SELECT location,
	round(population_density,0) as pop_density
FROM CovidDeaths
WHERE continent IS NOT NULL
GROUP BY location, population_density
ORDER BY population_density DESC
	OFFSET 0 ROWS FETCH NEXT 10 ROWS ONLY;

-- Countries that start and end with a
SELECT DISTINCT location AS countries
FROM CovidDeaths
WHERE location LIKE 'a%a';

-- Countries that are 6 characters long
SELECT DISTINCT location AS countries
FROM CovidDeaths
WHERE location LIKE '______';

-- total cases vs population for united states
SELECT
	location,
	date,
	total_cases,
	population,
	ROUND((total_cases/population)*100, 2) as CasePercentage
FROM CovidDeaths
WHERE continent IS NOT NULL
AND location LIKE '%states%'
ORDER BY location, date;

-- highest infect rate compared to population
SELECT
	location,
	population,
	MAX(total_cases)AS HighestInfectionCount,
	ROUND(MAX((total_cases/population)*100), 2) as PercentofPopulationAffected
FROM CovidDeaths
WHERE continent IS NOT NULL
GROUP BY location, population
ORDER BY PercentofPopulationAffected DESC;

-- death percentage each day since cases started being recorded
SELECT
	date,
	SUM(new_cases) as Cases,
	SUM(cast(new_deaths as int)) as Deaths,
	SUM(cast(new_deaths as int))/SUM(new_cases)*100 as DeathPercentage
FROM CovidDeaths
WHERE continent IS NOT NULL
GROUP BY date
ORDER BY date;

-- Joining CovidDeaths and CovidVacs, list of life expectancy for each country in Europe starting from highest to lowest and percentage of population fully vaccinated
SELECT cd.continent,
	cd.location,
	cv.life_expectancy,
	cd.population,
	ROUND(MAX(cv.people_fully_vaccinated)/ cd.population, 3) AS perc_fully_vaccinated
FROM CovidDeaths AS cd
INNER JOIN CovidVacs AS cv
ON cd.location = cv.location
WHERE cv.life_expectancy > 65
	AND cd.continent = 'Europe'
	AND cv.people_fully_vaccinated IS NOT NULL
GROUP BY cd.continent, cd.location, cv.life_expectancy, cd.population
ORDER BY cv.life_expectancy DESC;
