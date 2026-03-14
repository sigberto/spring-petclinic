# Testing Spring PetClinic

## Environment Setup

- The app requires Java 21. The VM may only have Java 17 pre-installed.
- To install Java 21: `sudo apt-get install -y openjdk-21-jdk`
- Set Java 21 before running: `export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64 && export PATH=$JAVA_HOME/bin:$PATH`
- Start the app: `./mvnw spring-boot:run` (uses in-memory H2 database by default, no external DB needed)
- The app runs at http://localhost:8080/

## Architecture Notes

- Pet types are data-driven, stored in the `types` DB table. Adding a new pet type only requires inserting a row in the seed data SQL files — no Java code changes.
- Seed data files exist for 3 database backends:
  - `src/main/resources/db/h2/data.sql` (default)
  - `src/main/resources/db/mysql/data.sql`
  - `src/main/resources/db/postgres/data.sql`
- The `PetTypeRepository.findPetTypes()` method loads all types from the DB. The UI dropdown in the pet form is populated dynamically.
- The `PetType` entity (`src/main/java/.../owner/PetType.java`) extends `NamedEntity` and has no type-specific logic.

## UI Testing Paths

### Testing Pet Types (e.g., adding a new pet type)
1. Navigate to http://localhost:8080/
2. Click "Find Owners" in the nav bar
3. Click "Find Owner" with empty last name to list all owners
4. Click on any owner (e.g., George Franklin)
5. Click "Add New Pet" button
6. The "Type" dropdown should list all pet types alphabetically
7. Fill in Name, Birth Date (mm/dd/yyyy format in Chrome), select the new Type
8. Click "Add Pet"
9. Verify redirect to owner details page with success message "New Pet has been Added"
10. Verify the new pet appears in the "Pets and Visits" section with the correct type

### Testing Pet Editing
1. On an owner's details page, click "Edit Pet" next to any pet
2. The edit form pre-fills with the pet's current data
3. Change the type dropdown and verify the update persists

## Common Issues

- If port 8080 is already in use, kill the existing process: `kill $(ss -tlnp | grep 8080 | grep -oP 'pid=\K[0-9]+')`
- `lsof` may not be available on the VM; use `ss -tlnp` instead to check port usage.
- The Maven enforcer plugin will reject builds if the Java version is below 21.

## Devin Secrets Needed

No secrets are required for local testing. The app uses an in-memory H2 database with seed data.
