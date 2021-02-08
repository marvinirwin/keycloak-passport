# Keycloak Passport Strategy - oAuth2/OIDC

This library offers a production-ready and maintained Keycloak Passport connector that offers the following key features:

- Use multiple realms in the same application (multi-tenancy)

- Use with oAuth2/Open ID Connect 'clients' in keycloak

- Fetch users' data from keycloak automatically via the JSON API

## Listed Keycloak Extension

Check it out on [keycloak.org](https://www.keycloak.org/extensions.html)

## Why? Hasn't this already been done?

To a certain extent, yes. There are about 3 to 4 repos that brand themselves as 'Keycloak Passport', yet not a single one of them is actively maintained and most of them are either completely empty, don't allow using multiple realms, only implement part of the protocol, and/or don't fetch the user's data from Keycloak. There also exists a dedicated NodeJS connector by the Keycloak project itself, however, it is unusable if you are seeking to have Keycloak as 'yet another' passport strategy in your app. This project fills that gap.

## Usage

## Install

```bash
npm install @exlinc/keycloak-passport
```

### Import

```javascript
import KeycloakStrategy from "@exlinc/keycloak-passport";
```

### Initialize

```javascript
// Register the strategy with passport
passport.use(
  "keycloak",
  new KeycloakStrategy(
    {
      host: process.env.KEYCLOAK_HOST,
      realm: process.env.KEYCLOAK_REALM,
      clientID: process.env.KEYCLOAK_CLIENT_ID,
      clientSecret: process.env.KEYCLOAK_CLIENT_SECRET,
      callbackURL: `/api${AUTH_KEYCLOAK_CALLBACK}`,
      authorizationURL: `${process.env.KEYCLOAK_ROOT}/realms/${process.env.KEYCLOAK_REALM}/protocol/openid-connect/auth`,
      tokenURL: `${process.env.KEYCLOAK_ROOT}/realms/${process.env.KEYCLOAK_REALM}/protocol/openid-connect/token`,
      userInfoURL: `${process.env.KEYCLOAK_ROOT}/realms/${process.env.KEYCLOAK_REALM}/protocol/openid-connect/userinfo`,
    },
    (accessToken, refreshToken, profile, done) => {
      // This is called after a successful authentication has been completed
      // Here's a sample of what you can then do, i.e., write the user to your DB
      User.findOrCreate({ email: profile.email }, (err, user) => {
        assert.ifError(err);
        user.keycloakId = profile.keycloakId;
        user.imageUrl = profile.avatar;
        user.name = profile.name;
        user.save((err, savedUser) => done(err, savedUser));
      });
    }
  )
);
```

### Routes

```javascript
router.get(
  routes.AUTH_KEYCLOAK,
  passport.authenticate("keycloak", DEFAULT_PASSPORT_OPTIONS)
);
router.get(
  routes.AUTH_KEYCLOAK_CALLBACK,
  passport.authenticate("keycloak", DEFAULT_PASSPORT_OPTIONS),
  AuthController.keyCloakSuccess
);
```

## Compatability with next-auth

There are some known issues with using this passportjs strategy with the latest versions of next-auth. Follow the discussion [here](https://github.com/exlinc/keycloak-passport/issues/1).

## Contributing/feedback

All forms of contribution are welcome via Issues and Pull-requests to this repo
