# <!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <title>عرض مقتطفات من أول 12 فيديو</title>
</head>
<body>
  <h2>مقتطفات أول ١٢ فيديو</h2>
  <div id="videos"></div>

  <script>
    const API_KEY = 'YOUR_YOUTUBE_API_KEY'; // ضع مفتاح API هنا
    const PLAYLIST_ID = 'PLknwEmKsW8OuTqUDaFRBiAViDZ5uI3VcE';
    const MAX_RESULTS = 12;

    async function fetchFirst12Details() {
      const url = new URL('https://www.googleapis.com/youtube/v3/playlistItems');
      url.search = new URLSearchParams({
        part: 'snippet',
        playlistId: PLAYLIST_ID,
        maxResults: MAX_RESULTS,
        key: API_KEY,

        fields: 'items(snippet(title,description,publishedAt,resourceId/videoId,thumbnails/medium/url))'
      });

      const resp = await fetch(url);
      if (!resp.ok) throw new Error(`خطأ HTTP: ${resp.status}`);
      const data = await resp.json();
      return data.items.map(item => {
        const s = item.snippet;
        return {
          title: s.title,
          desc: s.description,
          date: new Date(s.publishedAt).toLocaleDateString(),
          videoUrl: `https://www.youtube.com/watch?v=${s.resourceId.videoId}`,
          thumbnail: s.thumbnails.medium.url
        };
      });
    }

    function renderVideos(videos) {
      const container = document.getElementById('videos');
      videos.forEach(v => {
        const div = document.createElement('div');
        div.style.margin = '10px 0';
        div.innerHTML = `
          <img src="${v.thumbnail}" width="200" alt="Thumb">
          <a href="${v.videoUrl}" target="_blank"><strong>${v.title}</strong></a>
          <div><small>${v.date}</small></div>
          <p>${v.desc.slice(0, 100)}...</p>
        `;
        container.appendChild(div);
      });
    }


    fetchFirst12Details()
      .then(renderVideos)
      .catch(err => {
        console.error(err);
        document.getElementById('videos').innerText = 'خطأ أثناء التحميل.';
      });
  </script>
</body>
</html>