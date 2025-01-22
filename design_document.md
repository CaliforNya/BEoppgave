_Poker Club Turnamnet Manager_

# What do we want to achieve?

Project to create a tournament manager for a Poker Club. Only admin users (i.e., club managers) will have the ability to create tournaments. Regular users will be able to sign up for tournaments created by the admins, participate, and view details of ongoing or past tournaments.

## User Roles:

1.Admin Users "Floor"( +Club Managers):
-Can create, update, and delete tournaments.
-Can add and remove players to/from tournaments.
-Can assign tournaments as "active", "ongoing", or "completed".

2.Other Users (Players):
-Can sign up for tournaments created by admins.
-Can view details about tournaments (e.g., players, status, etc.).
-Can withdraw from a tournament if they decide not to participate

### Data managment

1.User Registration and Authentication:

-Admins and regular users will need to sign up and log in using an email and password.
-Admins will have additional rights to create and manage/edit tournaments.

2.Tournament Information:

-Name of the tournament.
-Date and time of the tournament
-Which game: nlh, nlh6max plo, plo5, RoE (checkbox)
-Bounty ? standard/progressive/mystery (checkbox)
-Additional information like buy-in, late registration,
-Bounty ? standard/progressive/mystery (checkbox)
-Maximum number of players allowed in the tournament.
-Current list of players signed up.
-Status of the tournament (e.g., "planned", " late reg." ,"ongoing", "completed").
-Information if rebuy will be avaiable

#### Frontend (View): (HTML, CSS, JavaScript)

1.Login Page

Users must log in with their credentials. Admin users will be identified and given access to create and manage tournaments automatically.

2.Home Page:

\*Displays a list of tournaments created by admins.

\*If a user is an admin, they will have a button to create a new tournament.

\*If a user is a regular player, they can only see tournaments they can join. ( see details button----> turnament detail modal + join btn )

3.Tournament Details Page: (modal?)

\*Displays details about a specific tournament.

\*Allows players to sign up or withdraw.

\*Shows the current player list and status (planned,late reg., ongoing, completed).

\*Admin will be able to add/remove players (and post results at the end?)

4.Tournament Creation Page (For Admin ):

Admins can enter details like tournament name, date/time, max players, and status.
Admins can submit the form to create a new tournament.

#### Backend :

1. User Authentication Controller

-Handle user login and registration.
-Verify user credentials with Firebase Authentication.
-Redirect users to appropriate dashboards (Admin View or Player View).

2. Tournament Management Controller (Admin Role)
   -Enable admins to create, update, and delete tournaments.
   -Manage player lists by adding or removing players.
   -Change tournament statuses (planned, ongoing, completed).
   -Validate tournament details before saving to the database.
   -Update Firestore with new or modified tournament data.
   -Fetch updated tournament lists and display them to the admin view.

3. Player Interaction Controller (Player Role)
   -Allow players to sign up for tournaments, view tournament details, or withdraw from participation.
   -Check for tournament capacity before allowing player sign-up.
   -Update player lists in the Firestore database.

##### Models and Data Flow:

1.User Model:

username: Unique identifier for the user.
email: User's email address.
password: Hashed password stored securely.
role:"creator","admin" or "user". Admins can create tournaments, while regular users can only sign up for them.

2.Tournament Model:

name: Name of the tournament.
date: Date and time of the tournament.
maxPlayers: Maximum number of players allowed in the tournament.
status: "planned", "ongoing","late registration" or "completed".
players: List of player IDs who have signed up for the tournament.
creatorId: ID of the user who created the tournament (should be an admin).
gameType:Type of the game in the tournament. Options could be "NLH", "NLH6max", "PLO", "PLO5", "RoE".
bountyType: Type of bounty in the tournament. Options could be "standard", "progressive", "mystery".
buyIn: The buy-in amount for the tournament .
rebuyAvaiable: Yes/No information

/example/
const tournamentExample = {
name: " Winter Circut 2025 NLH",
date: "15-03-2025 19:00",
maxPlayers: 100,
status: "planned",
players: ["player1", "player2", "player3"],
creatorId: "admin123",
gameType: "NLH",
bountyType: "progressive",
buyIn: 100
rebuyAvailable: true,
};

###### Additional information

*A regular user can sign up for multiple tournaments.
*An admin user can create and manage multiple tournaments.
\*A tournament can have many players but only one creator ( and many admins).

---

## DIAGRAMS/CHARTS

---

# Tournament Management Flowchart

::: mermaid

flowchart TD
A[Start] --> B[User logs in]
B --> C{Is the user an admin?}
C -- Yes --> D[Show admin dashboard]
C -- No --> E[Show player dashboard]

    D --> F[Admin chooses action]
    E --> G[Player chooses action]

    F --> H{Is the action to create or edit a tournament?}
    G --> I{Is the action to join a tournament?}

    H -- Yes --> J[Show tournament creation/edit form]
    H -- No --> I

    J --> L[Create or update tournament]
    L --> M[Show tournament creation/update confirmation]

    I -- Yes --> N[Show tournament details]
    I -- No --> T[Show updated player dashboard]

    N --> O{Is there space in the tournament?}
    O -- Yes --> P[Add player to tournament]
    O -- No --> Q[Show message about full tournament]
    P --> R[Show player added confirmation]
    Q --> S[Player selects another tournament]
    S --> T[Show updated tournament list]
    R --> T
    M --> U[Show updated admin dashboard]
    U --> W
    T --> W[End]

:::

# Entity-relation diagram for database relationships:

::: mermaid
erDiagram
User {
int UserId
string UserName
string HashPassword
string Role
}
Tournament {
int TournamentId
string TournamentName
DateTime TournamentDate
string Status
int MaxPlayers
string GameType
string BountyType
decimal BuyIn
bool RebuyAvailable
}
UserSignupTournamentRelations {
int UserId
int TournamentId
}
UserTournamentRoles {
int UserId
int TournamentId
string Role // Role can be 'creator', 'admin', 'participant'
}
%% Relationships
User ||--o{ UserSignupTournamentRelations : "Many-to-Many (Signups)"
UserSignupTournamentRelations ||--o{ Tournament : "Many-to-Many (Signups)"
User ||--o{ UserTournamentRoles : "Many-to-Many (Roles)"
UserTournamentRoles ||--o{ Tournament : "Many-to-Many (Roles)"
User ||--o| UserTournamentRoles : "One-to-One (Creator)"
UserTournamentRoles ||--o| Tournament : "One-to-One (Creator)"

:::

# Data Flow Diagram

::: mermaid
sequenceDiagram
participant httpRequest as HTTP Request
participant ApiDefaultRoute as API Default Route
participant TournamentController as Tournament Controller
participant Firebase as Firebase (Firestore)
participant Database as Firestore Database

    httpRequest ->>+ ApiDefaultRoute: /GET (Request for Tournaments Data)
    ApiDefaultRoute ->>+ TournamentController: Get active tournaments
    TournamentController ->>+ Firebase: Query Firestore for active tournaments
    Firebase ->>+ Database: Fetch active tournaments
    Database -->>- Firebase: Return list of tournaments
    Firebase -->>- TournamentController: Return tournaments data
    TournamentController -->>- ApiDefaultRoute: Return tournaments data to user
    ApiDefaultRoute -->>- httpRequest: Send tournaments data in response

:::

# Login token validation diagram

::: mermaid
sequenceDiagram
actor User
participant View(Index)
participant LoginController
participant LoginService
participant UserDatabase (Firebase)

    User ->>+ View(Index): "Enters Index View"
    View(Index) ->>+ LoginService: "Checks for existing token in cookies"
    LoginService ->>+ UserDatabase (Firebase): "Validates token"
    UserDatabase (Firebase) -->>- LoginService: "Returns validation result"
    LoginService -->>- View(Index): "Sends validation status (valid/invalid)"
    alt Token is valid
        View(Index) -->> User: "Displays authenticated view"
    else Token is invalid or absent
        View(Index) ->>+ User: "Displays Unauthenticated (anonymous) view"
    end

:::

# "Tokes doesnt exist" diagram

    ::: mermaid

    sequenceDiagram
    actor User
    participant View(Index)
    participant View(Login)
    participant LoginController
    participant FirebaseAuthService
    participant FirestoreDatabase

    User ->>+ View(Index): "Clicks Login"
    View(Index) ->>+ View(Login): "Redirects to Login View"
    View(Login) ->>+ LoginController: "Submits email and password /POST"
    LoginController ->>+ FirebaseAuthService: "Triggers Firebase Authentication login"
    FirebaseAuthService ->>+ FirebaseAuthService: "Validates credentials with Firebase Authentication"
    FirebaseAuthService -->>- LoginController: "Returns authentication result"
    alt Credentials are valid
        FirebaseAuthService ->>+ FirestoreDatabase: "Fetch user data from Firestore"
        FirestoreDatabase -->>- FirebaseAuthService: "Returns user data"
        FirebaseAuthService -->> LoginController: "Returns success with token"
        LoginController -->> View(Login): "Responds with success and token"
        View(Login) -->> User: "Displays success message and authenticated view"
    else Credentials are invalid
        FirebaseAuthService -->>- LoginController: "Returns error"
        LoginController -->>- View(Login): "Responds with error"
        View(Login) -->>- User: "Displays error feedback"
    end

:::

# "Create new user" diagram (with dbl verfication)

::: mermaid
sequenceDiagram
actor AnonymousUser
participant LoginController
participant FirebaseAuthService
participant FirestoreDatabase
participant EmailVerificationService

    AnonymousUser ->>+ LoginController: /POST /New (formData)
    LoginController ->>+ FirebaseAuthService: Validate required fields and create user
    alt validation succeeds
        FirebaseAuthService ->>+ FirebaseAuthService: Create new user with email and password
        FirebaseAuthService -->> LoginController: User created with Firebase Authentication
        LoginController ->>+ EmailVerificationService: Send email verification link
        EmailVerificationService -->> LoginController: Verification email sent
        LoginController ->>+ FirestoreDatabase: Insert additional user data (e.g., username, profile) into Firestore
        FirestoreDatabase -->> LoginController: OK (User data saved)
        LoginController -->> AnonymousUser: HTTP 201 Created (New user created, email verification required)
    else validation fails
        LoginController -->>- AnonymousUser: HTTP 400 Bad Request with error message
    end

:::

# Home page data flow diagram

::: mermaid

sequenceDiagram
actor User
participant EventController
participant Firestore
participant FirebaseAuth

    User ->>+ EventController: /GET request
    EventController ->>+ Firestore: Fetch public events
    Firestore -->>- EventController: Return public events

    alt User is authenticated
        EventController ->>+ FirebaseAuth: Verify authentication
        FirebaseAuth -->>- EventController: Return authenticated user
        EventController ->>+ Firestore: Fetch user-specific events (owned, admin, signed-up)
        Firestore -->> EventController: Return user-specific events
        EventController -->> User: Return JSON (public + user-specific events)
    else User is anonymous
        EventController -->>- User: Return JSON (public events only)
    end

:::

# New turnament diagram

::: mermaid

sequenceDiagram
actor User
participant TournamentController
participant DtoConstructor
participant FirebaseAuth
participant FirestoreDatabase

    User ->>+ TournamentController: /POST /newTournament (jsonData)
    TournamentController ->>+ FirebaseAuth: Check if user is authenticated
    alt User is authenticated
        FirebaseAuth -->>+ TournamentController: Return user info (userId)
        TournamentController ->>+ DtoConstructor: Validate and construct Tournament DTO from jsonData
        alt DTO validation succeeds
            DtoConstructor ->> FirestoreDatabase: Create new tournament with user as creator (userId)
            FirestoreDatabase -->> TournamentController: OK (Tournament created)
            FirestoreDatabase ->> FirestoreDatabase: Update relation table (Creator, Tournament)
            FirestoreDatabase -->> TournamentController: OK (Relation updated)
            TournamentController -->> User: HTTP 201 Created with tournament details and location
        else DTO validation fails
            DtoConstructor -->>- TournamentController: Validation error
            TournamentController -->>- User: HTTP 400 Bad Request with error message
        end
    else User is not authenticated
        TournamentController -->>- User: HTTP 401 Unauthorized (User needs to log in)
    end

:::

# Edit turnament diagram

::: mermaid

sequenceDiagram
actor User
participant TournamentController
participant AuthorizationService
participant DtoConstructor
participant FirebaseAuth
participant FirestoreDatabase

    User ->>+ TournamentController: /PATCH /{id} (jsonData)
    TournamentController ->>+ FirebaseAuth: Check if user is authenticated
    alt User is authenticated
        FirebaseAuth -->>+ TournamentController: Return user info (userId)
        TournamentController ->>+ AuthorizationService: Check if user is admin or owner of tournament
        alt User has editing privileges (Admin or Owner)
            AuthorizationService -->> TournamentController: Authorized
            TournamentController ->>+ DtoConstructor: Validate and construct Tournament DTO from jsonData
            alt DTO validation succeeds
                DtoConstructor ->> FirestoreDatabase: Pass DTO and Tournament ID for update
                FirestoreDatabase ->> FirestoreDatabase: Update tournament data with new values
                FirestoreDatabase -->> TournamentController: OK (Tournament updated)
                TournamentController -->> User: HTTP 200 OK with updated tournament details
            else DTO validation fails
                DtoConstructor -->> TournamentController: Validation error
                TournamentController -->> User: HTTP 400 Bad Request with error message
            end
        else User lacks privileges (Neither Admin nor Owner)
            AuthorizationService -->>- TournamentController: Unauthorized
            TournamentController -->>- User: HTTP 403 Forbidden with error message
        end
    else User is not authenticated
        TournamentController -->>- User: HTTP 401 Unauthorized (User needs to log in)
    end

:::

# Sign up to turnament diagram

::: mermaid
sequenceDiagram
actor User
participant TournamentController
participant AuthorizationService
participant FirebaseAuth
participant FirestoreDatabase

    User ->>+ TournamentController: /POST /Signup/{id} (signup request)
    TournamentController ->>+ FirebaseAuth: Check if user is authenticated
    alt User is authenticated
        FirebaseAuth -->>+ TournamentController: Return user info (userId)
        TournamentController ->>+ FirestoreDatabase: Check if max participants reached
        FirestoreDatabase -->> TournamentController: Return number of participants
        alt Max participants not reached
            TournamentController ->> FirestoreDatabase: Insert user-event relation (signup action)
            FirestoreDatabase ->> FirestoreDatabase: Add entry to participant relation table
            FirestoreDatabase -->> TournamentController: OK (User successfully signed up)
            TournamentController -->> User: HTTP 201 Created (Signup successful)
        else Max participants reached
            TournamentController -->>- User: HTTP 409 Conflict (Max participants reached)
        end
    else User is not authenticated
        TournamentController -->>- User: HTTP 401 Unauthorized (User needs to log in)
    end

:::
