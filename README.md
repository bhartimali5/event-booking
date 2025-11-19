# event-booking
A small event-booking REST API built with Go and Gin.
# REST API (Go + Gin)

Small event-management REST API built with Go and Gin.

## Quick links (source)
- Entry point: [main.go](main.go) — uses [`db.InitDB`](db/db.go) and [`db.CreateTables`](db/db.go) then registers routes via [`routes.RegisterRoutes`](routes/routes.go).
- Database: [db/db.go](db/db.go) (SQLite, file: `api.db`)
- Routes registry: [routes/routes.go](routes/routes.go)
- User handlers: [`routes.signUp`](routes/users.go), [`routes.login`](routes/users.go) — file: [routes/users.go](routes/users.go)
- Event handlers: [`routes.getEvents`](routes/events.go), [`routes.getEvenetByID`](routes/events.go), [`routes.createEvents`](routes/events.go), [`routes.updateEvent`](routes/events.go), [`routes.deleteEvent`](routes/events.go) — file: [routes/events.go](routes/events.go)
- Registration handlers: [`routes.registerForEvent`](routes/register.go), [`routes.cancelRegisterationForEvent`](routes/register.go) — file: [routes/register.go](routes/register.go)
- Models: [`models.User.Save`](models/user.go), [`models.User.ValidateCredentials`](models/user.go) — [models/user.go](models/user.go); [`models.Event.Save`](models/event.go), [`models.Event.Register`](models/event.go), [`models.Event.CancelRegistration`](models/event.go) — [models/event.go](models/event.go)
- Utils: password hashing [`utils.HashPassword`](utils/hash.go), [`utils.CheckPasswordHash`](utils/hash.go) — [utils/hash.go](utils/hash.go); JWT helpers [`utils.GenerateJWT`](utils/jwt.go), [`utils.VerifyToken`](utils/jwt.go) — [utils/jwt.go](utils/jwt.go)
- Middleware: auth middleware [`middlewares.AuthMiddleware`](middlewares/auth.go) — [middlewares/auth.go](middlewares/auth.go)
- API examples: [api-test/](api-test/) (sample HTTP requests)

## Prerequisites
- Go toolchain (project uses module `example.com/rest-api`)
- No external database required — uses SQLite file `api.db` (created by the app).

## Build & run
From project root:

```sh
go run
```
This will:

open / create api.db via [db.InitDB](db/db.go]
create tables via db.CreateTables
start the Gin server on port 8080
You can also build a binary:
```
go build -o rest-api 
./rest-api
```

##API (summary)
Public:

- POST /signup — handler: routes.signUp (create user; uses models.User.Save)
- POST /login — handler: routes.login (validate credentials via models.User.ValidateCredentials and return token via utils.GenerateJWT)
Authenticated (require header Authorization: <token> — validated by middlewares.AuthMiddleware which calls utils.VerifyToken):

- GET /events — routes.getEvents
- GET /events/:id — routes.getEvenetByID
- POST /events — routes.createEvents (creates event via models.Event.Save)
- PUT /events/:id — routes.updateEvent
- DELETE /events/:id — routes.deleteEvent
- POST /events/:id/register — routes.registerForEvent (register via models.Event.Register)
- DELETE /events/:id/register — routes.cancelRegisterationForEvent (cancel via models.Event.CancelRegistration)

  
See example requests in the api-test/ folder:

- api-test/signup.http
- api-test/login.http
- api-test/create-event.http
- api-test/register.http
- api-test/cancel-registration.http
- api-test/get-event.http
- api-test/get-event-by-id.http
- api-test/update-event.http
- api-test/delete-event.http
  
##Authentication details
- JWT secret defined in utils.JwtSecretKey.
- Token generation: utils.GenerateJWT.
- Token verification: utils.VerifyToken.
- Add header: Authorization: <token> for authenticated endpoints.
  
##Passwords
- Passwords are hashed with bcrypt before storing using utils.HashPassword.
- Credential checking uses utils.CheckPasswordHash.
  
##Database schema
Tables are created by db.CreateTables. Data is stored in api.db in project root.

##Notes & TODOs
- Input validation is basic; need to consider stronger request validation and better error messages.
- The JWT secret is hard-coded (utils/jwt.go) — move to env var.
- Add unit tests and CI.
  
