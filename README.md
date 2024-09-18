
# COVID '19 Data Analysis Project

This project explores various aspects of the global COVID-19 pandemic by analyzing datasets related to COVID-19 cases, deaths, and vaccinations. Using SQL queries, the analysis covers infection rates, death rates, population impacts, and vaccination rollouts across different countries and continents. The insights gained from this analysis aim to provide a better understanding of the spread and impact of COVID-19 worldwide.

## Project Objectives:
1. Analyze COVID-19 cases and death rates globally and by continent/country.
2. Determine the likelihood of death from COVID-19 in different regions.
3. Examine the percentage of the population infected with COVID-19 in each country.
4. Identify countries with the highest infection and death rates relative to their populations.
5. Analyze vaccination rollout and its impact on population immunity.
6. Create a database view for further visualizations of population vaccination rates.

## Key Analysis & Queries:

### 1. **COVID-19 Cases and Deaths by Continent**:
   The query pulls data on total COVID-19 cases and deaths for each continent to identify regions with the highest impact. The focus is on countries where COVID-19 significantly affected the population.

   ```sql
   SELECT location, date, total_cases, total_deaths, population
   FROM ProjectPortfolio..CovidDeaths
   WHERE continent IS NOT NULL
   ORDER BY location, date;
   ```

### 2. **Death Percentage by Country**:
   This analysis shows the likelihood of dying if a person contracts COVID-19 in a specific country. The query calculates the death percentage for countries like the United States.

   ```sql
   SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases) * 100 AS DeathPercentage
   FROM ProjectPortfolio..CovidDeaths
   WHERE location LIKE '%states%' AND continent IS NOT NULL
   ORDER BY location, date;
   ```

### 3. **Infection Rates Compared to Population**:
   This query analyzes the percentage of the population infected with COVID-19 in each country.

   ```sql
   SELECT location, date, population, total_cases, (total_cases/population) * 100 AS PercentPopulationInfected
   FROM ProjectPortfolio..CovidDeaths
   ORDER BY location, date;
   ```

### 4. **Countries with Highest Infection Rates**:
   Identifies the countries with the highest infection rates relative to their populations. The results are ordered by the percentage of population infected.

   ```sql
   SELECT location, population, MAX(total_cases) AS HighestInfectionCount, MAX((total_cases/population)) * 100 AS PercentPopulationInfected
   FROM ProjectPortfolio..CovidDeaths
   GROUP BY location, population
   ORDER BY PercentPopulationInfected DESC;
   ```

### 5. **Countries with Highest Death Counts**:
   A ranking of countries based on total COVID-19 death counts to determine which regions were most affected by fatalities.

   ```sql
   SELECT location, MAX(CAST(total_deaths AS INT)) AS TotalDeathCount
   FROM ProjectPortfolio..CovidDeaths
   WHERE continent IS NOT NULL
   GROUP BY location
   ORDER BY TotalDeathCount DESC;
   ```

### 6. **Global Numbers and Death Percentages**:
   This global overview calculates the total number of cases, deaths, and the death percentage across all continents.

   ```sql
   SELECT SUM(new_cases) AS Total_Cases, SUM(CAST(new_deaths AS INT)) AS Total_Deaths, SUM(CAST(new_deaths AS INT))/SUM(new_cases) * 100 AS DeathPercentage
   FROM ProjectPortfolio..CovidDeaths
   WHERE continent IS NOT NULL;
   ```

### 7. **Vaccination Analysis**:
   Examines the number of vaccinations administered and compares them with the population to estimate the percentage of the population vaccinated in each country.

   ```sql
   SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations, SUM(CAST(vac.new_vaccinations AS INT)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS RollingPeopleVaccinated
   FROM ProjectPortfolio..CovidDeaths dea
   JOIN ProjectPortfolio..CovidVaccinations vac ON dea.location = vac.location AND dea.date = vac.date
   WHERE dea.continent IS NOT NULL
   ORDER BY dea.location, dea.date;
   ```

### 8. **Creating a View for Visualizing Vaccination Data**:
   This query creates a view to store the data for population vaccination rates, allowing for future visualizations and analysis.

   ```sql
   CREATE VIEW PercentPopulationVaccinated AS
   SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations, SUM(CAST(vac.new_vaccinations AS INT)) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS RollingPeopleVaccinated
   FROM ProjectPortfolio..CovidDeaths dea
   JOIN ProjectPortfolio..CovidVaccinations vac ON dea.location = vac.location AND dea.date = vac.date
   WHERE dea.continent IS NOT NULL;
   ```

## Tools & Technologies:
- **SQL**: For data extraction, transformation, and analysis of COVID-19 case, death, and vaccination data.
- **Microsoft SQL Server**: Used to run queries and manage the database.
- **ProjectPortfolio Database**: The database holding the COVID-19 data used in this analysis.

## Insights & Recommendations:
1. **High Infection and Death Rates**: Countries like the United States and several others show high death percentages, indicating the severe impact of the pandemic in those regions.
2. **Vaccination Rollouts**: Countries with high vaccination rates relative to their populations can expect lower infection rates in future waves of the virus.
3. **Future Research**: Further analysis could examine the relationship between vaccination rates and reduced death percentages or explore the economic impact of these trends.

## How to Use:
You can explore the queries listed above to gain insights from the COVID-19 dataset. The project allows you to modify the SQL queries to analyze specific regions, countries, or time periods based on the dataset.
