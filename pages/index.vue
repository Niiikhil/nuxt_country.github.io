<template>
    <div class="d-flex flex-column align-items-center justify-content-center">

        <Head>
            <title>Countries</title>
            <meta name="description" content="Countries list" />
            <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.1/dist/css/bootstrap.min.css" rel="stylesheet"
                integrity="sha384-iYQeCzEYFbKjA/T2uDLTpkwGzCiq6soy8tYaI1GyVh/UjpbCx/TYkiZhlZB6+fzT"
                crossorigin="anonymous" />
            <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css"
                rel="stylesheet" />
        </Head>
        <!-- Navbar -->
        <div class="navbar w-100 d-flex align-items-center justify-content-center">
            <div class="heading">
                <button @click="fetchCountries" class="btn"
                    style="text-decoration: none; font-weight: bold; background: none; border: none; padding: 0; font-size: 34px;">
                    Countries
                </button>
            </div>
        </div>


        <!-- Search Form -->
        <form class="searchbox mt-3 mx-auto" @submit.prevent="onSearch">
            <div class="input-container position-relative w-100">
                <input v-model="searchQuery" type="text" class="form-control search-input"
                    placeholder="Search country" />
                <button type="button" class="search-icon btn position-absolute" @click="onSearch">
                    <i class="fas fa-search search-icon" @click="onSearch"> </i>
                    <!-- <font-awesome-icon :icon="['fas', 'magnifying-glass']" /> -->
                </button>
            </div>
        </form>




        <!-- Country List -->
        <div v-if="countries.length" class="mt-5 w-40">
            <div v-for="(country, index) in countries" :key="index"
                class="dynamic-countries mt-3 d-flex align-items-center border rounded p-3">
                <!-- Country Flag -->
                <div class="country-image me-3" style="width: 170px; height: 140px; overflow: hidden">
                    <img :src="country.flags?.png || ''" alt="flag" class="img-fluid"
                        style="width: 100%; height: 100%; object-fit: contain; border: none" />
                </div>

                <!-- Country Details -->
                <div class="detail-with-buttons flex-grow-1">
                    <h5 id="c-name">{{ country.name.common || "N/A" }}</h5>
                    <p id="currency" class="mb-1">{{ getCurrency(country) }}</p>
                    <p id="c-date" class="mb-2">{{ getLocalTime(country) }}</p>
                    <div class="buttons d-flex justify-content-between">
                        <button class="btn btn-outline-primary me-2 custom-button"
                            style="border: 3px solid #1500ff; color: #1500ff;" @click="showMap(country)">
                            Show Map
                        </button>

                        <button class="btn btn-outline-primary custom-button"
                            style="border: 3px solid #1500ff; color: #1500ff;" @click="showDetails(country)">
                            Details
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            countries: [],
            searchQuery: "",
            utcOffsetMapping: {

                "UTC+01:00": "Europe/Belgrade",
                "UTC+02:00": "Africa/Johannesburg",
                "UTC+03:00": "Africa/Nairobi",
                "UTC+04:00": "Asia/Dubai",
                "UTC+05:00": "Asia/Karachi",
                "UTC+05:30": "Asia/Kolkata",
                "UTC+06:00": "Asia/Almaty",
                "UTC+07:00": "Asia/Bangkok",
                "UTC+08:00": "Asia/Singapore",
                "UTC+09:00": "Asia/Tokyo",
                "UTC+10:00": "Australia/Sydney",
                "UTC+11:00": "Pacific/Noumea",
                "UTC+12:00": "Pacific/Fiji",
                "UTC-01:00": "Atlantic/Azores",
                "UTC-02:00": "Atlantic/South_Georgia",
                "UTC-03:00": "America/Argentina/Buenos_Aires",
                "UTC-04:00": "America/Caracas",
                "UTC-05:00": "America/New_York",
                "UTC-06:00": "America/Chicago",
                "UTC-07:00": "America/Denver",
                "UTC-08:00": "America/Los_Angeles",
                "UTC-09:00": "America/Anchorage",
                "UTC-10:00": "Pacific/Honolulu",
                "UTC-11:00": "Pacific/Midway",
                "UTC-12:00": "Pacific/Kwajalein"

            },
        };
    },
    methods: {
        fetchCountries() {
            const url = "https://restcountries.com/v3.1/all";
            fetch(url)
                .then((res) => res.json())
                .then((data) => {
                    this.countries = data;
                })
                .catch((err) => console.error("Error fetching countries:", err));
        },
        onSearch() {
            const query = this.searchQuery.trim();
            if (!query) return;

            const url = `https://restcountries.com/v3.1/name/${query}`;
            fetch(url)
                .then((res) => res.json())
                .then((data) => {
                    this.countries = data;
                })
                .catch((err) => console.error("Error searching country:", err));
        },
        getCurrency(country) {
            if (country.currencies) {
                const key = Object.keys(country.currencies)[0];
                return `Currency: ${country.currencies[key]?.name || "N/A"}`;
            }
            return "Currency: N/A";
        },
        getLocalTime(country) {
            const timezone = country.timezones?.[0];
            const mappedTimezone = this.utcOffsetMapping[timezone] || timezone;

            try {
                const options = {
                    timeZone: mappedTimezone,
                    day: "numeric",
                    month: "short",
                    year: "numeric",
                    hour: "2-digit",
                    minute: "2-digit",
                };
                // return new Date().toLocaleString("en-US", options);
                return `Current Date and Time: ${new Date().toLocaleString("en-US", options)}`;
            } catch (error) {
                return "Invalid timezone";
            }
        },
        showMap(country) {
            const countryName = country.name.common.toLowerCase();
            const mapUrl = `https://www.google.com/maps/place/${countryName}/`;
            window.open(mapUrl, "_blank");
        },
        showDetails(country) {
            const countryCode = country.cca3; // Get the 3-letter country code
            console.log("countryCode index", countryCode);
            this.$router.push(`/details?code=${countryCode}`); // Pass the countryCode as a query parameter
        }

    },
    mounted() {
        this.fetchCountries();
    },
};
</script>

<style scoped>
.navbar {
    /* background-color: #f8f9fa; */
    padding: 10px;
}

.heading h1 {
    margin: 0;
}

.dynamic-countries {
    background-color: #ffffff;
}

.dynamic-countries:hover {
    border: 2px solid #6f6f6f !important;
}

.buttons button {
    flex-grow: 1;
}

img {
    border: 1px solid #ccc;
    border-radius: 5px;
}

.custom-button:hover {
    background-color: #1500ff;
    color: white !important;
    border: 3px solid #1500ff;
}

.input-container {
    position: relative;
    width: 100%;
    max-width: 100%;
    /* Allow full width on smaller devices */
    margin: 0 auto;
}

.search-input {
    padding-right: 2.5rem;
    /* Add space for the icon inside the input */
    height: 40px;
    /* Smaller height for the input */
    width: 100%;
    /* Ensure the input takes full width */
}

.search-icon {
    position: absolute;
    top: 50%;
    right: 10px;
    /* Position the icon inside the input */
    transform: translateY(-50%);
    background: none;
    /* Remove button background */
    border: none;
    /* Remove button border */
    padding: 0;
    cursor: pointer;
    color: #6c757d;
    /* Default icon color */
}

.search-icon:hover {
    color: #0d6efd;
    /* Change icon color on hover */
}
</style>
