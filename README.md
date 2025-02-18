<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [videojs-schema](#videojs-schema)
  - [Options](#options)
  - [Description metadata](#description-metadata)
  - [Install](#install)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# videojs-schema

A plugin for the Brightcove Player to inject metadata from a Video Cloud video in to the page as [Schema.org](https://schema.org/VideoObject) structured data. It uses the JSON-LD format [supported by Google for video SEO](https://developers.google.com/search/docs/guides/intro-structured-data), e.g.

```html
<script type="application/ld+json">
{
  "@context":"http://schema.org/",
  "@type":"VideoObject",
  "name":"Big Buck Bunny",
  "description":"(c) copyright 2008, Blender Foundation / www.bigbuckbunny.org",
  "thumbnailUrl":"https://cf-images.eu-west-1.prod.boltdns.net/v1/static/906043040001/f7d56300-ffca-460d-8ff6-fef835c12b36/e284fdba-d15a-422f-bebc-6a355e3d4dd3/1280x720/match/image.jpg",
  "uploadDate":"2016-10-20",
  "@id":"http://players.brightcove.net/906043040001/5WnZb7ptr_default/index.html?videoId=ref:bunnyfull",
  "duration":"PT10M35S",
  "embedUrl":"http://players.brightcove.net/906043040001/5WnZb7ptr_default/index.html?videoId=1401169490001",
  "contentUrl": "https://server.com/file.mp4",
  "hasPart": [{
    "@type": "Clip",
    "name": "Cat jumps",
    "startOffset": 30,
    "url": "http://players.brightcove.net/906043040001/5WnZb7ptr_default/index.html?videoId=1401169490001?t=30"
  },
  {
    "@type": "Clip",
    "name": "Cat misses the fence",
    "startOffset": 60,
    "url": "http://players.brightcove.net/906043040001/5WnZb7ptr_default/index.html?videoId=1401169490001?t=60"
  }]
}
</script>
```

As seen by Google: https://search.google.com/structured-data/testing-tool/u/0/#url=http%3A%2F%2Fplayers.brightcove.net%2F906043040001%2F5WnZb7ptr_default%2Findex.html%3FvideoId%3Dref%3Abunnyfull

The embedUrl is only included if the social plugin is present and its `removeEmbed` option is not `true`.

## Options

`schemaId` - the value to use as `@id` in the metadata. This must be a unique URI which represents the video, but not necessarily a "real" URL. This is arbitrary and might be a value like `https://mydomain.com/videos/{id}` or `https://videos.mydomain.com#{id}` or `https://mydomain.com/article-about-video-{id}#the-video`.

Accepts `{id}`, `{referenceId}`, `{playerId}`,  `{embedId}` and `{accountId}` as macros.

Defaults to `https://players.brightcove.net/{accountId}/{playerId}_{embedId}/index.html?videoId={id}`

`keywords` - if `true`, include tags as keywords. Default is `false`.

`excludeTags` - array of tags to not include as keywords, e.g. `["youtubesync"]`

`baseObject` - an option object of properties onto which to build the video specific metadata. For example this could be used to include a publisher object:

```json
"baseObject": {
  "publisher": {
    "@type": "Organization",
    "name": "Publisher name",
    "logo": {
      "@type": "ImageObject",
      "url": "https://example.com/logo.jpg",
      "width": 600,
      "height": 60
    }
  }
}
```

`includeClips` - if `true`, includes a list of clips under a "hasPart" array, based on the CODE cue points within the video. Default is `false`. __POC; TESTS NEED TO BE ADDED.__

`includeContentUrl` - if `true`, includes a direct URL to the highest MP4 rendition of the video. Be careful with short-expiry TTL URLs. Default is `false`. __POC; TESTS NEED TO BE ADDED.__

`includeEmbedUrl` - if `false`, no embed url is included. Inlcuding thisURL may be expected by search engines. Default is `true`. 

`transcript` - if `true`, a captions or subtitles track with a matching language will be added as a transcript. Default is `false`.

`transcriptMatchAny` - if `true` and `transcript` is also `true`, a transcript will be added from the first usable track, if there is no language match. Default is `false`.

`preferLongDescription` - if `true`, use the long description if available. If `false`, or `true` and long description is not set then the (short) description field will be used. Default is `false`.

## Description metadata
By default, the short description field is used as the description text. If the `preferLongDescription` option is set, the long description will be used instead, falling back to short description if empty. If the short description (or both long and short descriptions with `preferLongDescription`) is empty, then the title is used since no description is considered invlaid schema.

## Install

Add [as a plugin to a player in Video Cloud Studio][plugins]:

- Under "plugins" click "add a plugin", 'custom plugin"
- Plugin name is `schema`
- Javascript URL is `https://cdn.jsdelivr.net/npm/videojs-schema/dist/videojs-schema.min.js`
- No CSS
- Leave blank unless using options above, in which case

```json
{
  "schemaId": "https://example.com/my/url/{id}"
}
```

- Save, then publish

[plugins]: [https://support.brightcove.com/configuring-player-plugins]
