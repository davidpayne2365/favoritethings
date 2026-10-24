# Favorite Things

A tiny site where people sign up, log in and post pictures of their favorite things. Single file: `index.html`.

- Accounts: Firebase Authentication (email + password). Turn on **Email/Password** under Authentication → Sign-in method.
- Posts: Firebase Realtime Database at `favorites/<id>`. Pictures are shrunk in the browser (max 900px JPEG) and stored in the post, so Firebase Storage is not needed.
- Same Firebase project as the MTG site (`mtgapp-aacbd`).

Add this inside the top-level `"rules": { ... }` of the Realtime Database rules, then Publish:

```json
"favorites": {
  ".read": true,
  "$id": {
    ".write": "auth != null && ((!data.exists() && newData.child('uid').val() === auth.uid) || (data.exists() && !newData.exists() && data.child('uid').val() === auth.uid))",
    ".validate": "newData.hasChildren(['uid', 'author', 'title', 'image', 'ts']) && newData.child('title').isString() && newData.child('title').val().length <= 80 && newData.child('image').isString() && newData.child('image').val().length < 400000 && newData.child('image').val().beginsWith('data:image/')"
  }
}
```

Anyone can read posts; only a logged-in user can add a post as themselves or delete their own.
