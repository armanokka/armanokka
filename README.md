{{ $ := .root }}
{{ $page := .page }}

{{ $author := "" }}
{{ if .author }}
  {{ $author = .author }}
{{ else }}
  {{ $author = $page.Params.author }}
{{end}}

{{ $person_page_path := (printf "/authors/%s" $author) }}
{{ $person_page := site.GetPage $person_page_path }}
{{ if not $person_page }}
  {{ errorf "Could not find an author page at `%s`. Please check the value of `author` in your About widget and create an associated author page if one does not already exist. See https://sourcethemes.com/academic/docs/page-builder/#about " $person_page_path }}
{{end}}
{{ $person := $person_page.Params }}
{{ $avatar := ($person_page.Resources.ByType "image").GetMatch "*avatar*" }}
{{ $avatar_shape := site.Params.avatar.shape | default "circle" }}

<!-- About widget -->
<div class="row">
  <div class="col-12 col-lg-4">
    <div id="profile">

      {{ if site.Params.avatar.gravatar }}
      <img class="avatar {{if eq $avatar_shape "square"}}avatar-square{{else}}avatar-circle{{end}}" src="https://s.gravatar.com/avatar/{{ md5 $person.email }}?s=270')" alt="{{$person_page.Title}}">
      {{ else if $avatar }}
      {{ $avatar_image := $avatar.Fill "270x270 Center" }}
      <img class="avatar {{if eq $avatar_shape "square"}}avatar-square{{else}}avatar-circle{{end}}" src="{{ $avatar_image.RelPermalink }}" alt="{{$person_page.Title}}">
      {{ end }}

      <div class="portrait-title">
        <h2>{{ $person_page.Title }}</h2>
        {{ with $person.role }}<h3>{{ . | markdownify | emojify }}</h3>{{ end }}

        {{ range $person.organizations }}
        <h3>
          {{ with .url }}<a href="{{ . }}" target="_blank" rel="noopener">{{ end }}
          <span>{{ .name }}</span>
          {{ if .url }}</a>{{ end }}
        </h3>
        {{ end }}
      </div>

      <ul class="network-icon" aria-hidden="true">
        {{ range $person.social }}
        {{ $pack := or .icon_pack "fas" }}
        {{ $pack_prefix := $pack }}
        {{ if in (slice "fab" "fas" "far" "fal") $pack }}
          {{ $pack_prefix = "fa" }}
        {{ end }}
        {{ $link := .link }}
        {{ $scheme := (urls.Parse $link).Scheme }}
        {{ $target := "" }}
        {{ if not $scheme }}
          {{ $link = .link | relLangURL }}
        {{ else if in (slice "http" "https") $scheme }}
          {{ $target = "target=\"_blank\" rel=\"noopener\"" }}
        {{ end }}
        <li>
          <a href="{{ $link | safeURL }}" {{ $target | safeHTMLAttr }}>
            <i class="{{ $pack }} {{ $pack_prefix }}-{{ .icon }} big-icon"></i>
          </a>
        </li>
        {{ end }}
      </ul>

    </div>
  </div>
  <div class="col-12 col-lg-8">

    {{/* Only display widget title in explicit instances of about widget, not in author pages. */}}
    {{ if and $page.Params.widget $page.Title }}<h1>{{ $page.Title | markdownify | emojify }}</h1>{{ end }}

    {{ $person_page.Content }}

    <div class="row">

      {{ with $person.interests }}
      <div class="col-md-5">
        <h3>{{ i18n "interests" | markdownify }}</h3>
        <ul class="ul-interests">
          {{ range . }}
          <li>{{ . | markdownify | emojify }}</li>
          {{ end }}
        </ul>
      </div>
      {{ end }}

      {{ with $person.education }}
      <div class="col-md-7">
        <h3>{{ i18n "education" | markdownify }}</h3>
        <ul class="ul-edu fa-ul">
          {{ range .courses }}
          <li>
            <!--https://github.com/catrincm/personal-website/blob/e06b3525f51a3dd2653578a29c9b5b253596ab1a/layouts/partials/widgets/about.html-->
            {{ if .course }}
            <i class="fa-li fas fa-graduation-cap"></i>
            <div class="description">
              <p class="course">{{ .course }}{{ with .year }}, {{ . }}{{ end }}</p>
              <p class="institution">{{ .institution | markdownify }}</p>
              <!--https://github.com/gcushen/hugo-academic/issues/1094#issuecomment-622200594-->
            </div>
            {{ end }}
            {{ if .certificate }}
            <i class="fa-li fas fa-certificate"></i>
            <div class="description">
              <p class="course">{{ .certificate }}{{ with .year }}, {{ . }}{{ end }}</p>
              <p class="institution">{{ .institution | markdownify }}</p>
              <!--https://github.com/gcushen/hugo-academic/pull/951#issue-255104712-->
            </div>
            {{ end }}
            
          </li>
          {{ end }}
        </ul>
      </div>
      {{ end }}

    </div>
  </div>
</div>
