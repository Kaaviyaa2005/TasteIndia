# TasteIndia — Indian Cuisine Discovery

A native Android recipe-discovery application built with Kotlin, Jetpack Compose and Material 3 for the TasteIndia Android intern assignment.

## Features

- Loads the Indian meal collection from TheMealDB V1.
- Search by meal name, sort A–Z/Z–A, and combine category, ingredient and favourites-only filters.
- Uses stable meal IDs for details navigation.
- Displays ingredients with measurements, instructions, tags, metadata, and validated source/video links.
- Persists favourite IDs locally with Room.
- Loading, error, empty and retry states; Coil loads recipe imagery.
- Light/dark Material 3 colour schemes and accessible favourite labels.
- Offline unit tests for ingredient normalization and ID intersection logic.

## Technology and toolchain

- Kotlin 2.0.21
- Android Gradle Plugin 8.7.3
- Gradle wrapper: generate/open with Android Studio's recommended Gradle version (Gradle 8.9 compatible with AGP 8.7.x)
- JDK 17
- compileSdk 35, targetSdk 35, minSdk 26
- Jetpack Compose + Material 3, Navigation Compose, Retrofit, OkHttp, Kotlin Serialization, Coroutines, Room, Coil
- JUnit 4

## Run it

1. Install Android Studio and JDK 17.
2. Open this folder (`TasteIndia`) in Android Studio.
3. Allow Gradle sync and install Android SDK Platform 35 if prompted.
4. Run the `app` configuration on an emulator or Android device.
5. Internet access is required for initial recipe data and detail lookups.

Command-line build/test (after installing Android SDK and configuring `ANDROID_HOME`):

```bash
gradle :app:assembleDebug
gradle :app:testDebugUnitTest
```

If you prefer a Gradle wrapper, use Android Studio's **Tools → AGP Upgrade Assistant** or generate a wrapper with a locally installed compatible Gradle distribution. A wrapper is not included because its binary JAR is environment-generated.

## Architecture

```text
com.tasteindia
├── MainActivity.kt              Compose screens and navigation graph
├── data
│   ├── MealModels.kt            API DTOs and ingredient normalization
│   ├── MealApi.kt               Retrofit endpoints and HTTP timeouts
│   ├── MealRepository.kt        Remote data access and in-memory detail cache
│   └── FavouriteStore.kt        Room entity, DAO and database
└── ui
    ├── TasteViewModel.kt        UI state, filter state, actions and orchestration
    └── ViewModelFactory.kt      ViewModel construction
```

The ViewModel owns the current search, filter, sort, result and favourite state. The repository owns API calls and caches meal details for the session. Room is the durable source for favourite IDs.

## Navigation route map

```text
Recipes ── meal ID ──> Recipe Details
Favourites ── meal ID ──> Recipe Details
```

The navigation route contains only `mealId`, never a serialized meal object.

## API endpoints

Base URL: `https://www.themealdb.com/api/json/v1/1/`

- `filter.php?a=Indian` — authoritative Indian base collection.
- `lookup.php?i={mealId}` — full details.
- `list.php?c=list` — categories.
- `list.php?a=list` — areas.
- `filter.php?i={ingredient}` — ingredient match IDs.
- `filter.php?c={category}` — category match IDs.

Only the public educational TheMealDB V1 API/test key `1` is used. No private key or other recipe API is included. The checked-in fixture is for offline review/testing; runtime recipe loading makes a real GET request.

## Indian-boundary filtering strategy

The base list always comes from `filter.php?a=Indian`. Category and ingredient endpoint results are converted to ID sets, then intersected with the base Indian collection before rendering. Search, favourites-only and sorting are applied locally to that base set. This prevents foreign-area meals returned by filter endpoints from entering the results.

## Persistence and caching

- Room stores favourite meal IDs and keeps them across relaunches.
- The Indian list and fetched detail objects are cached in memory for the current process.
- The fixture under `app/src/main/assets/fixtures/` supports offline review; the app currently does not automatically switch to fixture data when the API is offline.
- Ingredient filter options are populated from detail data and may take time on first load because TheMealDB has no single endpoint for all Indian ingredients.

## Assumptions, trade-offs and known limitations

- The educational API may occasionally be unavailable or rate-limited.
- This starter implementation uses a single app module and a lightweight ViewModel factory rather than Hilt to keep the intern assignment proportional.
- Room persists favourites, but recipe detail payloads are cached in memory only. Opening details offline after process death is not guaranteed.
- The public API's filter endpoints do not combine filters, so IDs are intersected locally.
- A fresh Android Studio/Gradle sync and device build should be run before submission. This generated package has not been built in the target Android environment here.
- The project does not include a Gradle wrapper JAR or screenshots. Capture screenshots from a running emulator and add them under `screenshots/` before submission.
- Git history must be created incrementally by the candidate; no synthetic history is included in this ZIP.

## Tests

Run:

```bash
gradle :app:testDebugUnitTest
```

Tests are deterministic and do not call the network. They cover ingredient normalization, missing ingredient fields, Indian ID boundary intersection, and case-insensitive sorting.

## Attribution

- Recipe data and recipe images: [TheMealDB](https://www.themealdb.com/api.php). Review the provider's current educational-use terms before submitting.
- Android Jetpack/Compose, Retrofit, OkHttp, Kotlin Serialization, Room, Coil and JUnit are third-party libraries; see their respective official documentation and licenses.

## Time spent

Record your actual focused implementation time before submission: **[fill in honestly]**.

## AI/tool-use disclosure

This starter code and documentation were generated with AI assistance. Before submitting, review and modify the code, run the build and tests, add screenshots, record your actual time, and update this disclosure to explain what you personally reviewed, changed, and verified. You should be able to explain and modify every file during the interview.
