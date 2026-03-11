-- Create table for covid deaths.
Alter table CovidDeaths
Alter column population Type numeric,
Alter column total_cases type numeric,
Alter column new_cases type numeric,
Alter column new_cases_smoothed type numeric,
Alter column total_deaths type numeric,
Alter column new_deaths type numeric,
Alter column new_deaths_smoothed type numeric,
Alter column total_cases_per_million type numeric,
Alter column new_cases_per_million type numeric,
Alter column new_cases_smoothed_per_million type numeric,
Alter column total_deaths_per_million type numeric,
Alter column new_deaths_per_million type numeric,
Alter column new_deaths_smoothed_per_million type numeric,
Alter column reproduction_rate type numeric,
Alter column icu_patients type numeric,
Alter column icu_patients_per_million type numeric,
Alter column hosp_patients type numeric,
Alter column hosp_patients_per_million type numeric,
Alter column weekly_icu_admissions type numeric,
Alter column weekly_icu_admissions_per_million type numeric,
Alter column weekly_hosp_admissions type numeric,
Alter column weekly_hosp_admissions_per_million type numeric;

--copy coviddeaths FROM '/Users/akhtarrasoolkhan/Downloads/CovidDeaths.csv' with CSV HEADER ENCODING 'UTF8';

CREATE TABLE CovidVaccinations (
    iso_code TEXT,
    continent TEXT,
    location TEXT,
    date DATE,
    total_tests NUMERIC,
    new_tests NUMERIC,
    total_tests_per_thousand NUMERIC,
    new_tests_per_thousand NUMERIC,
    new_tests_smoothed NUMERIC,
    new_tests_smoothed_per_thousand NUMERIC,
    positive_rate NUMERIC,
    tests_per_case NUMERIC,
    tests_units TEXT,
    total_vaccinations NUMERIC,
    people_vaccinated NUMERIC,
    people_fully_vaccinated NUMERIC,
    total_boosters NUMERIC,
    new_vaccinations NUMERIC,
    new_vaccinations_smoothed NUMERIC,
    total_vaccinations_per_hundred NUMERIC,
    people_vaccinated_per_hundred NUMERIC,
    people_fully_vaccinated_per_hundred NUMERIC,
    total_boosters_per_hundred NUMERIC,
    new_vaccinations_smoothed_per_million NUMERIC,
    new_people_vaccinated_smoothed NUMERIC,
    new_people_vaccinated_smoothed_per_hundred NUMERIC,
    stringency_index NUMERIC,
    population_density NUMERIC,
    median_age NUMERIC,
    aged_65_older NUMERIC,
    aged_70_older NUMERIC,
    gdp_per_capita NUMERIC,
    extreme_poverty NUMERIC,
    cardiovasc_death_rate NUMERIC,
    diabetes_prevalence NUMERIC,
    female_smokers NUMERIC,
    male_smokers NUMERIC,
    handwashing_facilities NUMERIC,
    hospital_beds_per_thousand NUMERIC,
    life_expectancy NUMERIC,
    human_development_index NUMERIC,
    excess_mortality_cumulative_absolute NUMERIC,
    excess_mortality_cumulative NUMERIC,
    excess_mortality NUMERIC,
    excess_mortality_cumulative_per_million NUMERIC
);

Select * from Coviddeaths
order by location, date

Select * from Covidvaccinations
order by location, date

Select location, date, total_cases, new_cases, total_deaths, population
from Coviddeaths
order by location, date

--Looking at Total Cases vs Total Deaths (Death Percentage)
Select location, date, total_cases, total_deaths, Round((total_deaths/total_cases) *100,2) as DeathPercentage
from Coviddeaths
order by location, date

--Shows likelihood of dying if you contract covid in your country
Select location, date, total_cases, total_deaths, (total_deaths/total_cases) *100 as DeathPercent
from Coviddeaths
where location like '%India%'
order by location, date

--Looking at Total cases vs Population
--Shows what percentage of population got Covid
Select location, date, population, total_cases, Round((total_cases/population) *100,2) as PercentPopulationInfected
from Coviddeaths
where location like '%India%'
order by location, date

--Looking at Countries with highest infection rate compared to Population
Select location, population, max(total_cases) as HighestInfectCount, Round((Max(total_cases/population)) *100,2) as PercentPopulationInfected
from Coviddeaths
Group by location, population
order by PercentPopulationInfected desc

--Showing Countries with Highest Death count per Population
Select location, max(total_deaths) as TotalDeathCount
from Coviddeaths
where continent is not null and total_deaths is not null
Group by location
order by TotalDeathCount desc

--Let's break things by continents:
--Showing Continents with Highest Death count per Population
Select continent, max(total_deaths) as TotalDeathCount
from Coviddeaths
where continent is not null
Group by continent
order by TotalDeathCount desc


--Global Numbers

Select sum(new_cases) as total_cases, sum(new_deaths) as total_deaths, COALESCE(Round((sum(new_deaths)/Nullif (sum(new_cases), 0)*100),2)) as DeathPercentage
from Coviddeaths
--group by date
--order by date, deathpercentage

--Looking at Total Population vs Vaccination 

--With CTE

With PopvsVac (continent, location, date, population,new_vaccinations,rollingpeoplevaccinated)
as
(
Select coviddeaths.continent, coviddeaths.location, coviddeaths.date, coviddeaths.population, covidvaccinations.new_vaccinations,
sum(covidvaccinations.new_vaccinations) over (Partition by coviddeaths.Location order by coviddeaths.location, coviddeaths.date) as RollingPeopleVaccinated
from Coviddeaths inner join Covidvaccinations
on coviddeaths.location = covidvaccinations.location
and coviddeaths.date = covidvaccinations.date
where coviddeaths.continent is not null and new_vaccinations is not null
--order by location, date
)
Select *, round((rollingpeoplevaccinated/population) * 100,2) from popvsvac

--TEMP Table

Drop table if exists PercentPopulationVaccinated;

Create temp table PercentPopulationVaccinated
(Coninent text,
Location text,
date date,
Population numeric,
New_Vaccinations numeric,
 RollingPeopleVaccinated numeric);
 
 Insert into PercentPopulationVaccinated
 Select coviddeaths.continent, coviddeaths.location, coviddeaths.date, coviddeaths.population, covidvaccinations.new_vaccinations,
sum(covidvaccinations.new_vaccinations) over (Partition by coviddeaths.Location order by coviddeaths.location, coviddeaths.date) as RollingPeopleVaccinated
from Coviddeaths inner join Covidvaccinations
on coviddeaths.location = covidvaccinations.location
and coviddeaths.date = covidvaccinations.date
--where coviddeaths.continent is not null and new_vaccinations is not null
--order by location, date
select *, round((rollingpeoplevaccinated/population) * 100,2) from PercentPopulationVaccinated

--Creating View to store data for later visualization

Create view PercentPopulationVaccinatedd
as Select coviddeaths.continent, coviddeaths.location, coviddeaths.date, coviddeaths.population, covidvaccinations.new_vaccinations,
sum(covidvaccinations.new_vaccinations) over (Partition by coviddeaths.Location order by coviddeaths.location, coviddeaths.date) as RollingPeopleVaccinated
from Coviddeaths inner join Covidvaccinations
on coviddeaths.location = covidvaccinations.location
and coviddeaths.date = covidvaccinations.date
where coviddeaths.continent is not null and new_vaccinations is not null

select * from PercentPopulationVaccinatedd
