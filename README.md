# Sound System

Lightweight audio manager for Unity: named-sound lookup, fading, crossfading
music, per-category volume mixing (SFX vs music), and random-pitch /
random-clip playback.

## Install into a project

**Option A — local package (best while you're still actively developing it):**

1. Put this whole `SoundSystem` folder somewhere on disk, e.g. next to your
   game project (NOT inside its `Assets` folder):

   ```
   Projects/
   ├── MyGame/
   │   └── Packages/manifest.json
   └── SoundSystem/
       └── package.json
   ```

2. In `MyGame/Packages/manifest.json`, add a line under `"dependencies"`:

   ```json
   "com.programzer0.soundsystem": "file:../../SoundSystem"
   ```

   (adjust the relative path to wherever you actually put the folder)

3. Unity will import it as a package. Edits you make to the scripts show up
   immediately in the project — no re-import step.

**Option B — git package (once it's stable and you want to reuse it across
multiple game repos without keeping folders in sync manually):**

1. Push this `SoundSystem` folder as its own git repo.
2. In any project's `manifest.json`:

   ```json
   "com.programzer0.soundsystem": "https://github.com/ProgramZer0/SoundManager.git"
   ```

3. Tag releases (`v1.0.0`, `v1.1.0`, ...) and pin a project to one with
   `#v1.0.0` at the end of the URL, so updating the package for one game
   doesn't silently change behavior in another.

## Usage

1. Add a `SoundManager` component to an object in your scene (an empty
   `GameObject` named `SoundManager` works well — make it persist across
   scenes with `DontDestroyOnLoad` if you want music to keep playing).
2. Fill in the `sounds` array in the Inspector — one `Sound` entry per clip,
   each with a unique `name`.
3. Call it from anywhere:

   ```csharp
   using ProgramZer0.SoundSystem;

   soundManager.Play("Jump");
   soundManager.PlayMusic("MainTheme", fadeTime: 1.5f);
   soundManager.SetSoundMod(0.8f);
   ```

## Notes on this version

- The original script had an unused `[SerializeField] GameManger GM;` field
  referencing a game-specific class. It wasn't used anywhere in the class
  body, so it was removed — keeping it would have forced every project using
  this package to also define a class named exactly `GameManger` just to
  compile. If you actually need manager access inside `SoundManager`, prefer
  exposing a `UnityEvent` or an interface the game project implements, rather
  than a direct reference — that keeps the package decoupled from any one
  game's code.
- Both classes are wrapped in the `ProgramZer0.SoundSystem` namespace so they won't
  collide with any other `Sound`/`SoundManager` class you (or an asset store
  package) might have in a different project.

## Possible future improvements

- `Array.Find` does a linear scan over `sounds` on every `Play` call. Fine
  for a small roster; if you end up with hundreds of sounds, swap to a
  `Dictionary<string, Sound>` built once in `Awake`.
