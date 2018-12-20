1. How many records does each airline class have? Use \$project to show result as { class:
   "Z", total: 999 }

// query

> db.airlines.aggregate( [ { $group: {_id: "$class", total: {$sum: 1} }}, {$project: {class: "$_id", total: 1, _id: 0}} ] )

// result

{ "total" : 140343, "class" : "F" }
{ "total" : 23123, "class" : "L" }
{ "total" : 5683, "class" : "P" }
{ "total" : 17499, "class" : "G" }

2. What are the top 3 destination cities outside of the United States (destCountry field, not
   included) with the highest average passengers count? Show result as { "avgPassengers" :
   2312.380, "city" : "Minsk, Belarus" }

// query

> db.airlines.aggregate( [ { $group: {_id: "$destCity", avgPassengers: {$sum: "$passengers"} }}, {$project: {city: "$_id", avgPassengers: 1, _id: 0}}, {$sort: { avgPassengers: -1 }} , { $limit: 3 } ] )

// result

{ "avgPassengers" : 29526582, "city" : "Atlanta, GA" }
{ "avgPassengers" : 28260668, "city" : "Chicago, IL" }
{ "avgPassengers" : 25524911, "city" : "New York, NY" }

3. Which carriers provide flights to Latvia (destCountry)? Show result as one document {
   "\_id" : "Latvia", "carriers" : [ "carrier1", " carrier2", …] }

// query

> db.airlines.aggregate( [ {$match: {"destCountry": "Latvia"}},{ $group: {_id: "$destCountry", carriers: {$push: "$carrier"} } }] )

// result

{ "\_id" : "Latvia", "carriers" : [ "JetClub AG", "Blue Jet SP Z o o", "Uzbekistan Airways", "Uzbekistan Airways", "Uzbekistan Airways", "Uzbekistan Airways", "Uzbekistan Airways", "Uzbekistan Airways", "Uzbekistan Airways" ] }

4. What are the carriers which flue the most number of passengers from the United State to either
   Greece, Italy or Spain? Find top 10 carriers, but provide the last 7 carriers (do not include the
   first 3). Show result as { "\_id" : "<carrier>", "total" : 999}

// query

> db.airlines.aggregate( [ {$match: {"originCountry": "United States"}}, {$match: {$or: [{"destCountry": "Spain"},{"destCountry":"Greece"}, {"destCountry":"Italy"}]}},{ $group: {\_id: "$carrier", total: {$sum: 1} } }, {$limit: 10}, {$skip: 3}] )

// result

{ "\_id" : "United Air Lines Inc.", "total" : 45 }
{ "\_id" : "Global Jet Luxembourg, Sa", "total" : 1 }
{ "\_id" : "Gainjet Aviation SA", "total" : 1 }
{ "\_id" : "Meridiana S.p.A", "total" : 6 }
{ "\_id" : "Lauda Motion GmbH", "total" : 1 }
{ "\_id" : "Jet Aviation Flight Services, Inc.", "total" : 4 }
{ "\_id" : "Albinati Aeronautics SA", "total" : 3 }

5. Find the city (originCity) with the highest sum of passengers for each state (originState)
   of the United States (originCountry). Provide the city for the first 5 states ordered by state
   alphabetically (you should see the city for Alaska, Arizona and etc). Show result as {
   "totalPassengers" : 999, "location" : { "state" : "abc", "city" : "xyz"
   } }

// query

> db.airlines.aggregate( [{$match: {"originCountry": "United States"}}, { $group: {_id:{city: "$originCity", state: "$originState"}, totalPassengers: {$sum: "$passengers"} } }, {$sort: {"_id.state": 1}} ] )

// result

{ "\_id" : { "city" : "Muscle Shoals, AL", "state" : "Alabama" }, "totalPassengers" : 3830 }
{ "\_id" : { "city" : "Gulf Shores, AL", "state" : "Alabama" }, "totalPassengers" : 15 }
{ "\_id" : { "city" : "Montgomery, AL", "state" : "Alabama" }, "totalPassengers" : 88186 }
{ "\_id" : { "city" : "Anniston, AL", "state" : "Alabama" }, "totalPassengers" : 295 }
{ "\_id" : { "city" : "Gadsden, AL", "state" : "Alabama" }, "totalPassengers" : 0 }
{ "\_id" : { "city" : "Dothan, AL", "state" : "Alabama" }, "totalPassengers" : 27241 }
{ "\_id" : { "city" : "Decatur, AL", "state" : "Alabama" }, "totalPassengers" : 0 }
{ "\_id" : { "city" : "Mobile, AL", "state" : "Alabama" }, "totalPassengers" : 170651 }
{ "\_id" : { "city" : "Talladega, AL", "state" : "Alabama" }, "totalPassengers" : 0 }
{ "\_id" : { "city" : "Tuscaloosa, AL", "state" : "Alabama" }, "totalPassengers" : 635 }
{ "\_id" : { "city" : "Auburn, AL", "state" : "Alabama" }, "totalPassengers" : 30 }
{ "\_id" : { "city" : "Huntsville, AL", "state" : "Alabama" }, "totalPassengers" : 298132 }
{ "\_id" : { "city" : "Birmingham, AL", "state" : "Alabama" }, "totalPassengers" : 760120 }
{ "\_id" : { "city" : "Tanacross, AK", "state" : "Alaska" }, "totalPassengers" : 57 }
{ "\_id" : { "city" : "Galbraith Lake, AK", "state" : "Alaska" }, "totalPassengers" : 491 }
{ "\_id" : { "city" : "Clover Bay, AK", "state" : "Alaska" }, "totalPassengers" : 204 }
{ "\_id" : { "city" : "Adak Island, AK", "state" : "Alaska" }, "totalPassengers" : 1219 }
{ "\_id" : { "city" : "Wasilla, AK", "state" : "Alaska" }, "totalPassengers" : 8 }
{ "\_id" : { "city" : "Tokeen, AK", "state" : "Alaska" }, "totalPassengers" : 5 }
{ "\_id" : { "city" : "Moser Bay, AK", "state" : "Alaska" }, "totalPassengers" : 5 }
Type "it" for more
