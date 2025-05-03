# MonoGame.Extended2.VideoPlayback

MonoGame VideoPlayback forked from the excellent [hozuki/MonoGame.Extended2](https://github.com/hozuki/MonoGame.Extended2). This one is in a standalone version and minimum dependencies for MonoGame 3.8.1.303.

Bring your own **LGPL FFMpeg libraries** for LGPL compliancy (described below). This is the second big difference from [hozuki/MonoGame.Extended2](https://github.com/hozuki/MonoGame.Extended2), this doesn't reference the GPL version of FFmpeg dependencies.

PLEASE REFERS TO [hozuki/MonoGame.Extended.VideoPlayback README](https://github.com/hozuki/MonoGame.Extended2/blob/master/Sources/MonoGame.Extended.VideoPlayback/README.md) FOR MORE INFORMATION

| Windows |
| ------------|
 | <img src="https://raw.githubusercontent.com/hozuki/OpenMLTD.Projector/master/media/VideoPlayback/screenshots/screenshot2.png" width="640" height="360" /> |

## Projects

- Video Playback: A `Video` and `VideoPlayer` implementation for MonoGame (DesktopGL and WindowsDX) using FFmpeg.
- Demo: A minimal example code to play a video

## Overview

MonoGame.Extended.VideoPlayback mainly exposes two classes:

- `MonoGame.Extended.Framework.Media.Video`
- `MonoGame.Extended.Framework.Media.VideoPlayer`

These are the video-related classes for MonoGame/XNA. MonoGame runtimes targeting Windows (DirectX), macOS, iOS and Android all have corresponding
classes, but DesktopGL does not. So you can't play videos in MonoGame if you use DesktopGL target, unless you use some other supporting libraries. There is [one](https://github.com/brundows/XnaFFmpegDecoder), but it is rather incomplete.

Now with this library, you are able to play video using the same API as on other platforms in your game. For WindowsDX users, you can explicitly use the classes provided in this project, instead of the native ones (rely on Windows native codecs):

```csharp
using Video = MonoGame.Extended.Framework.Media.Video;
using VideoPlayer = MonoGame.Extended.Framework.Media.VideoPlayer;
```

The techniques, such as video-audio synchronization and independent thread rendering, can also be applied to elsewhere. You can consider this as a demonstration of building a video player upon FFmpeg under the context of .NET technologies.

## Usage

**Requirements**:

- [.NET 8.0](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)
- MonoGame (= 3.8.1.303)
- FFmpeg binaries (6.1, though earlier versions may work with corresponding FFmpeg.AutoGen)

**Preparation**:

You will need to build the LGPL version of the FFmpeg dependencies, but [FFmpeg-Builds](https://github.com/BtbN/FFmpeg-Builds) build them for us.

Sdcb.FFmpeg 6.1 targets conveniently FFmpeg 6.1 binaries, so go to [release tab from FFmpeg-Builds](https://github.com/BtbN/FFmpeg-Builds/releases) and look for the latest _ffmpeg-n6.1-latest-win64-lgpl-shared-6.1.zip_. **Make sure to take the lgpl version, not the gpl one!**

Example:  [ffmpeg-n6.1-latest-win64-lgpl-shared-6.1.zip](https://github.com/BtbN/FFmpeg-Builds/releases/download/latest/ffmpeg-n6.1-latest-win64-lgpl-shared-6.1.zip)

Take all bin/*.dll, and add them as copy items for your startup projet to copy them in the output folder when built.

**Game code:**

`Game1.cs`:

```csharp
VideoPlayer videoPlayer;
Video video;

protected override void LoadContent() {
    // The creation of VideoPlayer is a little different from standard implementations,
    // because Game.Instance is internal so we can't get the graphics device of the running game
    // within a class other than Game, without using reflection.

    // You can, however, use the no parameters version of the constructor like the comment below.
    // You will receive a compiler warning about why not doing that.

    // videoPlayer = new VideoPlayer();

    videoPlayer = new VideoPlayer(GraphicsDevice);
    video = VideoHelper.LoadFromFile("some_video.mp4");

    videoPlayer.Play(video);
}

protected override void UnloadContent() {
    videoPlayer.Stop();
    video.Dispose();
    videoPlayer.Dispose();
}

protected override void Draw(GameTime gameTime) {
    // Frame and audio synchronization is automatic.
    var texture = videoPlayer.GetTexture();

    if (texture != null) {
        spriteBatch.Begin();

        var destRect = new Rectangle(0, 0, WindowWidth, WindowHeight);
        spriteBatch.Draw(texture, destRect, Color.White);

        spriteBatch.End();
    }

    // Do NOT dispose the obtained texture. It is a image cache so it is not recreated every call.

    base.Draw(gameTime);
}
```

## Limitations

Hey this is as is. Mostly fitted for my specific project with target framework net8.0-windows, with MonoGame target platform WindowsDX and forced in x64. But this should work with other targets too. Please refer to [hozuki/MonoGame.Extended.VideoPlayback README](https://github.com/hozuki/MonoGame.Extended2/blob/master/Sources/MonoGame.Extended.VideoPlayback/README.md) for more!

## License

BSD 3-Clause License
