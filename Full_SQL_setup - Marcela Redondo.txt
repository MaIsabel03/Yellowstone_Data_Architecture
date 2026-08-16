-- =============================================
-- Yellowstone Wildlife Database - Full SQL Setup
-- =============================================

-- Drop tables if they already exist
DROP TABLE IF EXISTS Wildlife_Weather_Impact, Observation_Period, Visitors, Weather, Park_Locations, Wildlife CASCADE;

-- ========== Wildlife Table ==========
CREATE TABLE Wildlife (
    Species_ID SERIAL PRIMARY KEY,
    Species_Name VARCHAR(100) UNIQUE,
    Population_Description TEXT,
    Population_Min INT,
    Population_Max INT
);

-- ========== Observation_Period Table ==========
CREATE TABLE Observation_Period (
    Observation_ID SERIAL PRIMARY KEY,
    Species_ID INT,
    Season VARCHAR(50),
    FOREIGN KEY (Species_ID) REFERENCES Wildlife(Species_ID)
);

-- ========== Weather Table ==========
CREATE TABLE Weather (
    Weather_ID SERIAL PRIMARY KEY,
    Observation_Date DATE,
    Tmax INT,
    Tmin INT,
    Prcp FLOAT,
    Field FLOAT
);

-- ========== Visitors Table (Normalized) ==========
CREATE TABLE Visitors (
Visitor_ID SERIAL PRIMARY KEY,
Visit_Year INT NOT NULL,
Annual_Total INT,
Location_ID INT,
FOREIGN KEY (Location_ID) REFERENCES Park_Locations(Location_ID)
);

-- ========== Monthly_Visitors Table ==========
CREATE TABLE Monthly_Visitors (
Visitor_ID INT,
Month VARCHAR(15),
Visitor_Count INT,
PRIMARY KEY (Visitor_ID, Month),
FOREIGN KEY (Visitor_ID) REFERENCES Visitors(Visitor_ID)
);

-- ========== Trigger Function to Update Annual_Total ==========
CREATE OR REPLACE FUNCTION update_annual_total()
RETURNS TRIGGER AS $$
BEGIN
    UPDATE Visitors
    SET Annual_Total = (
        SELECT SUM(Visitor_Count)
        FROM Monthly_Visitors
        WHERE Visitor_ID = NEW.Visitor_ID
    )
    WHERE Visitor_ID = NEW.Visitor_ID;

    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

========== Create trigger on Monthly_Visitors ==========
CREATE TRIGGER trg_update_annual_total
AFTER INSERT OR UPDATE ON Monthly_Visitors
FOR EACH ROW
EXECUTE FUNCTION update_annual_total();

-- ========== Wildlife_Weather_Impact Table ==========
CREATE TABLE Wildlife_Weather_Impact (
    Impact_ID SERIAL PRIMARY KEY,
    Species_ID INT,
    Weather_ID INT,
    Location_ID INT,
    Visibility_Level VARCHAR(50),
    FOREIGN KEY (Species_ID) REFERENCES Wildlife(Species_ID),
    FOREIGN KEY (Weather_ID) REFERENCES Weather(Weather_ID)
    -- Location_ID FK will be added after Park_Locations table is created
);

-- ========== Park_Locations Table ==========
CREATE TABLE Park_Locations (
    Location_ID SERIAL PRIMARY KEY,
    Location_Name VARCHAR(100),
    Region VARCHAR(50),
    Main_Attraction VARCHAR(100)
);

-- Now add foreign key to Wildlife_Weather_Impact for Location_ID
ALTER TABLE Wildlife_Weather_Impact
ADD CONSTRAINT fk_location_id FOREIGN KEY (Location_ID) REFERENCES Park_Locations(Location_ID);

-- =============================================
-- Sample Analytical Queries
-- =============================================

-- Find species seen in both Spring and Fall
SELECT DISTINCT w.Species_Name, w.Population_Min, w.Population_Max 
FROM Wildlife w 
JOIN Observation_Period o1 ON w.Species_ID = o1.Species_ID 
JOIN Observation_Period o2 ON w.Species_ID = o2.Species_ID 
WHERE o1.Season = 'Spring' AND o2.Season = 'Fall'; 

-- High visibility locations and attractions
SELECT DISTINCT p.Location_Name, p.Main_Attraction
FROM Park_Locations p
JOIN Wildlife_Weather_Impact wwi ON p.Location_ID = wwi.Location_ID
WHERE wwi.Visibility_Level = 'High';

-- Compare July vs. December visitor numbers by year
SELECT Visit_Year, Jul AS July_Visitors, "Dec" AS December_Visitors
FROM Visitors
ORDER BY Visit_Year ASC;

-- Average precipitation by visibility level
SELECT wwi.Visibility_Level, ROUND(AVG(w.Prcp)::numeric, 2) AS Avg_Precipitation
FROM Wildlife_Weather_Impact wwi
JOIN Weather w ON wwi.Weather_ID = w.Weather_ID
GROUP BY wwi.Visibility_Level
ORDER BY Avg_Precipitation DESC;

-- Wildlife visibility in Summer vs. Winter
SELECT DISTINCT w.Species_Name, o.Season, wwi.Visibility_Level
FROM Wildlife w
JOIN Observation_Period o ON w.Species_ID = o.Species_ID
JOIN Wildlife_Weather_Impact wwi ON w.Species_ID = wwi.Species_ID
WHERE o.Season = 'Summer'

UNION

SELECT DISTINCT w.Species_Name, o.Season, wwi.Visibility_Level
FROM Wildlife w
JOIN Observation_Period o ON w.Species_ID = o.Species_ID
JOIN Wildlife_Weather_Impact wwi ON w.Species_ID = wwi.Species_ID
WHERE o.Season = 'Winter';

-- =============================================
-- Role-Based Access Control
-- =============================================

-- Create roles/groups (NOLOGIN = groups only)
CREATE ROLE researchers NOLOGIN;
CREATE ROLE park_staff NOLOGIN;
CREATE ROLE admin_team NOLOGIN;

-- Create users and assign to groups
CREATE USER emily_researcher WITH PASSWORD 'research123';
GRANT researchers TO emily_researcher;

CREATE USER alice_staff WITH PASSWORD 'staff123';
GRANT park_staff TO alice_staff;

CREATE USER bob_admin WITH PASSWORD 'admin123';
GRANT admin_team TO bob_admin;

-- Permissions for researchers: read-only access
GRANT SELECT ON visitors, weather, wildlife, park_locations, observation_period, wildlife_weather_impact TO researchers;

-- Permissions for park staff: read + update/insert on visitors and weather
GRANT SELECT ON visitors, weather, wildlife, park_locations, observation_period, wildlife_weather_impact TO park_staff;
GRANT INSERT, UPDATE ON visitors, weather TO park_staff;

-- Permissions for admin team: full control
GRANT ALL PRIVILEGES ON visitors, weather, wildlife, park_locations, observation_period, wildlife_weather_impact TO admin_team;
