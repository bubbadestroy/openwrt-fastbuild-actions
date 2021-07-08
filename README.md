<article class="markdown-body entry-content container-lg" itemprop="text"><h1><a id="user-content-buildwrt" class="anchor" aria-hidden="true" href="#buildwrt"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>BuildWrt </h1>
<blockquote>
<p>Original OpenWrt-FastBuild-Actions </p>
</blockquote>
<p><a href="/tete1030/openwrt-fastbuild-actions/blob/master/README_CN_OUTDATED.md">Chinese description description </a>(outdated of the old version, it is not recommended to read, welcome to on the Wiki page <a href="https://github.com/tete1030/openwrt-fastbuild-actions/wiki/%E4%B8%AD%E6%96%87%E8%AF%B4%E6%98%8E%EF%BC%88WIP%EF%BC%89">Chinese description (WIP) </a>help improve the ) </p>
<p>This project accelerates OpenWrt building process in GitHub Actions. </p>
<p>Building OpenWrt in Github Actions can be very convenient for users who want to upgrade or modify their routers frequently. Despite convenience, users have to wait for hours for even slight changes, because cache from previous buildings is not recycled. </p>
<p>By storing cache in docker images, BuildWrt significantly decreases compiling duration in Github Actions. You can use Docker Hub or other Docker registries as the storage. </p>
<ul>
<li><a href="#features-overview">Features Overview </a></li>
<li><a href="#usage">Usage </a>
<ul>
<li><a href="#setup">Setup </a>
<ul>
<li><a href="#secrets-page">Secrets page </a></li>
</ul>
</li>
<li><a href="#examples">Examples </a></li>
<li><a href="#building">Building </a></li>
<li><a href="#advanced-usage">Advanced usage </a>
<ul>
<li><a href="#re-create-builder">Re-create builder </a></li>
<li><a href="#rebase-your-builder">Rebase your builder </a></li>
</ul>
</li>
<li><a href="#trigger-methods">Trigger methods </a></li>
<li><a href="#building-options">Building options </a>
<ul>
<li><a href="#examples-of-using-building-options">Examples of using building options </a></li>
</ul>
</li>
<li><a href="#multiple-target-profiles">Multiple target profiles </a>
<ul>
<li><a href="#default-profile">Default profile </a></li>
</ul>
</li>
<li><a href="#manually-adding-packages">Manually adding packages </a></li>
</ul>
</li>
<li><a href="#details">Details </a>
<ul>
<li><a href="#building-process-explained">Building process explained </a>
<ul>
<li><a href="#first-time-building">First-time building </a></li>
<li><a href="#following-buildings">Following buildings </a></li>
</ul>
</li>
<li><a href="#squashing-strategy">Squashing Strategy </a></li>
</ul>
</li>
<li><a href="#debugging-and-manually-configuring">Debugging and manually configuring </a>
<ul>
<li><a href="#important-directories">Important directories </a></li>
</ul>
</li>
<li><a href="#faqs">FAQs </a>
<ul>
<li><a href="#why-all-targets-seem-triggered-when-only-some-are-intended">Why all targets seem triggered when only some are intended? </a></li>
</ul>
</li>
<li><a href="#todo">Everything </a></li>
<li><a href="#acknowledgments">Acknowledgments </a></li>
<li><a href="#license">License </a></li>
</ul>
<h2><a id="user-content-features-overview" class="anchor" aria-hidden="true" href="#features-overview"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Features Overview </h2>
<ul>
<li>Building cache recycle </li>
<li>Multiple target profiles </li>
<li>Flexible pre-building customization
 <ul>
<li>Building config file ( <code>.config</code>) </li>
<li>Feeds config file ( <code>feeds.conf</code>) </li>
<li>Individual packages to be installed from Git repositories </li>
<li>Files to be copied to build root </li>
<li>Patches to be applied onto build root </li>
<li>Shell scripts to be executed before compiling </li>
</ul>
</li>
<li>Runtime customization &amp; debug
 <ul>
<li>Shell accessing through SSH or Web page (allow  <code>make menuconfig</code>) </li>
<li>Debug checkpoint </li>
<li>Failure fallback checkpoint </li>
</ul>
</li>
<li>Runtime job options
 <ul>
<li>re-create builder </li>
<li>packages only </li>
</ul>
</li>
<li>Abundant trigger methods
 <ul>
<li>Push event (only changed targets are built) </li>
<li>Commands in commit messages </li>
<li>Deployment events (you can use  <a href="https://github.com/tete1030/github-repo-dispatcher">tete1030/github-repo-dispatcher </a>) </li>
<li>Repository dispatch events (you can use  <a href="https://github.com/tete1030/github-repo-dispatcher">tete1030/github-repo-dispatcher </a>) </li>
<li>Repo been starred by yourself </li>
<li>Scheduled cron jobs </li>
</ul>
</li>
</ul>
<h2><a id="user-content-usage" class="anchor" aria-hidden="true" href="#usage"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Usage </h2>
<h3><a id="user-content-setup" class="anchor" aria-hidden="true" href="#setup"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Setup </h3>
<ol>
<li>Sign up for  <a href="https://github.com/features/actions/signup">GitHub Actions </a></li>
<li>Fork this repo </li>
<li><strong>Register a Docker Hub account </strong> and get your  <strong>personal access token </strong>. Fill your username and the token into the forked repo's  <strong>Settings-&gt;Secrets </strong> page. Use  <code>docker_username</code> for your username, and  <code>docker_password</code> for your token. See  <a href="#secrets-page">Secrets page </a> for correct settings. </li>
<li><em>(Necessary when debugging or runtime configuring) </em> Set either  <code>SLACK_WEBHOOK_URL</code>,  <code>TMATE_ENCRYPT_PASSWORD</code> or both in the Secrets page. Refer to  <a href="#debugging-and-manually-configuring">Debugging and manually configuring </a>. See  <a href="#secrets-page">Secrets page </a> for correct settings. </li>
<li>Add your building target
 <ol>
<li>Copy the  <code>user/example</code> folder, rename it (refered to by  <code>${TARGET_NAME}</code>) and put it in  <code>user</code> folder. This is your target folder. </li>
<li>Customize  <code>user/${TARGET_NAME}/settings.ini</code>. </li>
<li>Put your  <code>.config</code>file to <code>user/${TARGET_NAME}/config.diff</code>. </li>
<li><em>(Optional) </em> Add into  <code>user/${TARGET_NAME}/packages.txt</code> extra packages that are not in feeds. </li>
<li><em>(Optional) </em> Add into  <code>user/${TARGET_NAME}/custom.sh</code> any shell script to be executed right before compiling. </li>
<li><em>(Optional) </em> Add into  <code>user/${TARGET_NAME}/patches/</code> any patch to be applied onto the OpenWrt folder. </li>
<li><em>(Optional) </em> Add into  <code>user/${TARGET_NAME}/files/</code> any file you want to be copied into the OpenWrt folder (an exception:  <code>.config</code> is not copied) </li>
</ol>
</li>
<li>Add your  <code>${TARGET_NAME}</code> to  <code>jobs.build.strategy.matrix.target</code> section in  <code>.github/workflows/build-openwrt.yml</code> ( and to  <code>jobs.squash.strategy.matrix.target</code> of  <code>.github/workflows/squash.yml</code> if you are using squash strategy) </li>
</ol>

<h4><a id="user-content-secrets-page" class="anchor" aria-hidden="true" href="#secrets-page"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Secrets page </h4>
<p><a target="_blank" rel="noopener noreferrer" href="/tete1030/openwrt-fastbuild-actions/blob/master/docs/imgs/secrets.png"><img src="/tete1030/openwrt-fastbuild-actions/raw/master/docs/imgs/secrets.png" alt="Secrets page" style="max-width:100%;"></a></p>
<h3><a id="user-content-examples" class="anchor" aria-hidden="true" href="#examples"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Examples </h3>
<p>See my own setup: </p>
<p><a href="https://github.com/tete1030/openwrt-fastbuild-actions/tree/sample">https://github.com/tete1030/openwrt-fastbuild-actions/tree/sample </a></p>
<h3><a id="user-content-building" class="anchor" aria-hidden="true" href="#building"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Building </h3>
<p>The first-time building generally takes  <strong>1.5~3 hours </strong> depending on your config. </p>
<p>After the first-time building, only  <strong>15 minutes ~ 1 hour </strong> is needed, depending on your config and how much your config has changed. </p>
<ol>
<li>Make your changes </li>
<li><strong>Commit and push </strong> your changes. This will automatically trigger a building instance for changed targets. </li>
<li>Wait for the job to finish. </li>
<li>Collect your files in the  <code>Artifacts</code> menu of the job's log page. </li>
</ol>
<p>Many other methods allow you to trigger a building. Refer to  <a href="#trigger-methods">Trigger methods </a></p>
<h3><a id="user-content-advanced-usage" class="anchor" aria-hidden="true" href="#advanced-usage"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Advanced usage </h3>
<h4><a id="user-content-re-create-builder" class="anchor" aria-hidden="true" href="#re-create-builder"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Re-create builder </h4>
<p>If you have largely modified your configs, e.g. changed the repo or branch of OpenWrt, building may fail because old invalid cache could interfere with compiling. </p>
<p>It's better to completely re-create your builder. This means no previous cache is used, and all source code will be updated (if no version specified). This behaves the same as a first-time building. You can set the  <code>rebuild</code> building option to achieve this. For usage of building options, refer to  <a href="#manually-trigger-building-and-its-options">Manually trigger building and its options </a>. </p>
<h4><a id="user-content-rebase-your-builder" class="anchor" aria-hidden="true" href="#rebase-your-builder"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Rebase your builder </h4>
<p>This is faster than re-create a builder. Technically it reuses the builder from a recent first-time building instead of last building. It allow you to shrink the size of your builder without a complete rebuild. You can enable the  <code>rebase</code> option. </p>
<h3><a id="user-content-trigger-methods" class="anchor" aria-hidden="true" href="#trigger-methods"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Trigger methods </h3>
<p>By default, you can trigger building by </p>
<ul>
<li>Push your changes. BuildWrt will detect if any file affecting a target has been changed. If so the target will be built; otherwise skipped. </li>
<li>Commands in commit messages. Include  <code>#target=${TARGET_NAME}#</code> in the message of the last commit in a push. </li>
<li>Deployment events. Support building any commit/branch/tag. Use  <a href="https://github.com/tete1030/github-repo-dispatcher">tete1030/github-repo-dispatcher </a> to send the event.
 <ul>
<li>Install  <a href="https://github.com/tete1030/github-repo-dispatcher">tete1030/github-repo-dispatcher </a></li>
<li>Click "Deploy" button at the top right corner </li>
<li>Fill your Github personal access token in the "Token" prompt </li>
<li>Specify your commit/branch/tag in the "Ref" prompt (e.g.  <code>master</code>) </li>
<li>Fill  <code>build</code> in the "Task" prompt </li>
<li>Fill  <code>{"target": "${TARGET_NAME}"}</code> in the "Payload" prompt (the content should comply with JSON format) </li>
</ul>
</li>
<li>Repository dispatch events. Use  <a href="https://github.com/tete1030/github-repo-dispatcher">tete1030/github-repo-dispatcher </a> to send the event.
 <ul>
<li>Install  <a href="https://github.com/tete1030/github-repo-dispatcher">tete1030/github-repo-dispatcher </a></li>
<li>Click "Repo Dispatch" button at the top right corner </li>
<li>Fill your Github personal access token in the "Token" prompt </li>
<li>Fill  <code>build</code> in the "Type" prompt </li>
<li>Fill  <code>{"target": "${TARGET_NAME}"}</code> in the "Payload" prompt (in JSON) </li>
</ul>
</li>
</ul>
<p>If enabled, triggering is also possible when </p>
<ul>
<li>Your repo is starred by yourself. This makes the "Star" button act as a building trigger for yourself. Enable this by adding the following into the  <code>on</code> section of  <code>.github/workflows/build-openwrt.yml</code>:
 <div class="highlight highlight-source-yaml position-relative"><pre>  <span class="pl-ent">watch </span>:
     <span class="pl-ent">types </span>:  <span class="pl-s">[started] </span></pre><div class="zeroclipboard-container position-absolute right-0 top-0">
    <clipboard-copy aria-label="Copy" class="ClipboardButton btn js-clipboard-copy m-2 p-0 tooltipped-no-delay" data-copy-feedback="Copied!" data-tooltip-direction="w" value="  watch:
    types: [started]
" tabindex="0" role="button">
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-clippy js-clipboard-clippy-icon m-2">
    <path fill-rule="evenodd" d="M5.75 1a.75.75 0 00-.75.75v3c0 .414.336.75.75.75h4.5a.75.75 0 00.75-.75v-3a.75.75 0 00-.75-.75h-4.5zm.75 3V2.5h3V4h-3zm-2.874-.467a.75.75 0 00-.752-1.298A1.75 1.75 0 002 3.75v9.5c0 .966.784 1.75 1.75 1.75h8.5A1.75 1.75 0 0014 13.25v-9.5a1.75 1.75 0 00-.874-1.515.75.75 0 10-.752 1.298.25.25 0 01.126.217v9.5a.25.25 0 01-.25.25h-8.5a.25.25 0 01-.25-.25v-9.5a.25.25 0 01.126-.217z"></path>
</svg>
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-check js-clipboard-check-icon color-text-success d-none m-2">
    <path fill-rule="evenodd" d="M13.78 4.22a.75.75 0 010 1.06l-7.25 7.25a.75.75 0 01-1.06 0L2.22 9.28a.75.75 0 011.06-1.06L6 10.94l6.72-6.72a.75.75 0 011.06 0z"></path>
</svg>
    </clipboard-copy>
  </div></div>
</li>
<li>Scheduled cron job. Enable this by adding the following into the  <code>on</code> section of  <code>.github/workflows/build-openwrt.yml</code>:
 <div class="highlight highlight-source-yaml position-relative"><pre>  <span class="pl-c"><span class="pl-c"># </span> '0 0 * * 0' means sunday midnight </span>
  <span class="pl-c"><span class="pl-c"># </span> Examples of cron: https://crontab.guru/examples.html </span>
  <span class="pl-ent">schedule </span>:
    -  <span class="pl-ent">cron </span>:  <span class="pl-s"><span class="pl-pds">' </span>0 0 * * 0 <span class="pl-pds">' </span></span></pre><div class="zeroclipboard-container position-absolute right-0 top-0">
    <clipboard-copy aria-label="Copy" class="ClipboardButton btn js-clipboard-copy m-2 p-0 tooltipped-no-delay" data-copy-feedback="Copied!" data-tooltip-direction="w" value="  # '0 0 * * 0' means sunday midnight
  # Examples of cron: https://crontab.guru/examples.html
  schedule:
    - cron: '0 0 * * 0'
" tabindex="0" role="button">
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-clippy js-clipboard-clippy-icon m-2">
    <path fill-rule="evenodd" d="M5.75 1a.75.75 0 00-.75.75v3c0 .414.336.75.75.75h4.5a.75.75 0 00.75-.75v-3a.75.75 0 00-.75-.75h-4.5zm.75 3V2.5h3V4h-3zm-2.874-.467a.75.75 0 00-.752-1.298A1.75 1.75 0 002 3.75v9.5c0 .966.784 1.75 1.75 1.75h8.5A1.75 1.75 0 0014 13.25v-9.5a1.75 1.75 0 00-.874-1.515.75.75 0 10-.752 1.298.25.25 0 01.126.217v9.5a.25.25 0 01-.25.25h-8.5a.25.25 0 01-.25-.25v-9.5a.25.25 0 01.126-.217z"></path>
</svg>
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-check js-clipboard-check-icon color-text-success d-none m-2">
    <path fill-rule="evenodd" d="M13.78 4.22a.75.75 0 010 1.06l-7.25 7.25a.75.75 0 01-1.06 0L2.22 9.28a.75.75 0 011.06-1.06L6 10.94l6.72-6.72a.75.75 0 011.06 0z"></path>
</svg>
    </clipboard-copy>
  </div></div>
</li>
</ul>
<h3><a id="user-content-building-options" class="anchor" aria-hidden="true" href="#building-options"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Building options </h3>
<blockquote>
<p>Refer to  <a href="#trigger-methods">Trigger methods </a> for how to include options </p>
</blockquote>
<p>Building options are possible when building is triggered by </p>
<ul>
<li>Commands in commit messages. Include  <code>#OPTION_NAME#</code> in commit messages. E.g.,  <code>#target=x86_64##debug#</code> (merging two '#' is as well acceptable:  <code>#target=x86_64#debug#</code>). </li>
<li>Deployment events. Include  <code>"OPTION_NAME": true</code> in the JSON Payload. E.g.  <code>{"target": "x86_64", "debug": true}</code>. </li>
<li>Repository dispatch events. Include  <code>"OPTION_NAME": true</code> in the JSON Payload. E.g.  <code>{"target": "x86_64", "debug": true}</code></li>
</ul>
<p>All boolean options are by default  <code>false</code>. The following are options available. </p>
<ul>
<li><code>target</code>(string): the build target name </li>
<li><code>debug</code>(bool): entering tmate during and after building, allowing you to SSH into the docker container and Actions. See  <a href="#debug-and-manually-configure">Debug and manually configure </a> for detailed usage. </li>
<li><code>push_when_fail</code>(bool): always save the builder even if the building process failed. Not recommended to use </li>
<li><code>rebuild</code>(bool): re-create the building environment completely </li>
<li><code>rebase</code>(bool): use first-time builder instead of last builder </li>
<li><code>update_repo</code>(bool): update source of main repo. It could fail if any tracked file of the repo has changed. </li>
<li><code>update_feeds</code>(bool): update source of feed packages and manually added packages. It could fail if any tracked file changed. </li>
<li><code>package_only</code>(bool): only build packages, no firmware </li>
</ul>
<h4><a id="user-content-examples-of-using-building-options" class="anchor" aria-hidden="true" href="#examples-of-using-building-options"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Examples of using building options </h4>
<p>The following are examples for re-creating builder. </p>
<p><strong>Through commit message </strong></p>
<ol>
<li>Save all the files you changed </li>
<li>At the last commit before push, commit with message:
 <div class="snippet-clipboard-content position-relative"><pre><code>Some commit message #target=x86_64#rebuild#
</code></pre><div class="zeroclipboard-container position-absolute right-0 top-0">
    <clipboard-copy aria-label="Copy" class="ClipboardButton btn js-clipboard-copy m-2 p-0 tooltipped-no-delay" data-copy-feedback="Copied!" data-tooltip-direction="w" value="Some commit message #target=x86_64#rebuild#
" tabindex="0" role="button">
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-clippy js-clipboard-clippy-icon m-2">
    <path fill-rule="evenodd" d="M5.75 1a.75.75 0 00-.75.75v3c0 .414.336.75.75.75h4.5a.75.75 0 00.75-.75v-3a.75.75 0 00-.75-.75h-4.5zm.75 3V2.5h3V4h-3zm-2.874-.467a.75.75 0 00-.752-1.298A1.75 1.75 0 002 3.75v9.5c0 .966.784 1.75 1.75 1.75h8.5A1.75 1.75 0 0014 13.25v-9.5a1.75 1.75 0 00-.874-1.515.75.75 0 10-.752 1.298.25.25 0 01.126.217v9.5a.25.25 0 01-.25.25h-8.5a.25.25 0 01-.25-.25v-9.5a.25.25 0 01.126-.217z"></path>
</svg>
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-check js-clipboard-check-icon color-text-success d-none m-2">
    <path fill-rule="evenodd" d="M13.78 4.22a.75.75 0 010 1.06l-7.25 7.25a.75.75 0 01-1.06 0L2.22 9.28a.75.75 0 011.06-1.06L6 10.94l6.72-6.72a.75.75 0 011.06 0z"></path>
</svg>
    </clipboard-copy>
  </div></div>
</li>
<li>Push </li>
</ol>
<p><strong>Through Repo Dispatch or Deployment events </strong></p>
<ol>
<li>Open your forked repo </li>
<li>Click "Repo Dispatch" or "Deploy" at the top right corner (require installing  <a href="https://github.com/tete1030/github-repo-dispatcher">tete1030/github-repo-dispatcher </a>) </li>
<li>Fill  <code>build</code> in "Type/Task" prompt </li>
<li>If using "Deploy" trigger, fill your branch/tag/commit in "Ref" prompt (e.g.  <code>master</code>) </li>
<li>Fill  <code>{"target": "x86_64", "rebuild": true}</code> in "Payload" prompt </li>
</ol>
<h3><a id="user-content-multiple-target-profiles" class="anchor" aria-hidden="true" href="#multiple-target-profiles"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Multiple target profiles </h3>
<p>You can have multiple target profiles. They are arranged in </p>
<div class="snippet-clipboard-content position-relative"><pre><code>user
├── default                         # Default profile settings
│   └── ...
├── target1                         # First target
│   ├── config.diff                 # From .config
│   ├── custom.sh                   # Scripts to be executed before compiling
│   ├── files                       # Files to be copied to OpenWrt buildroot dir, arranged in same structure
│   │   └── somefile
│   └── settings.ini                # Settings
└── target2
    ├── config.diff
    ├── custom.sh
    ├── packages.txt
    ├── patches                     # Patches to be applied onto OpenWrt buildroot dir
    │   └── 001-somepatch.patch
    └── settings.ini
</code></pre><div class="zeroclipboard-container position-absolute right-0 top-0">
    <clipboard-copy aria-label="Copy" class="ClipboardButton btn js-clipboard-copy m-2 p-0 tooltipped-no-delay" data-copy-feedback="Copied!" data-tooltip-direction="w" value="user
├── default                         # Default profile settings
│   └── ...
├── target1                         # First target
│   ├── config.diff                 # From .config
│   ├── custom.sh                   # Scripts to be executed before compiling
│   ├── files                       # Files to be copied to OpenWrt buildroot dir, arranged in same structure
│   │   └── somefile
│   └── settings.ini                # Settings
└── target2
    ├── config.diff
    ├── custom.sh
    ├── packages.txt
    ├── patches                     # Patches to be applied onto OpenWrt buildroot dir
    │   └── 001-somepatch.patch
    └── settings.ini
" tabindex="0" role="button">
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-clippy js-clipboard-clippy-icon m-2">
    <path fill-rule="evenodd" d="M5.75 1a.75.75 0 00-.75.75v3c0 .414.336.75.75.75h4.5a.75.75 0 00.75-.75v-3a.75.75 0 00-.75-.75h-4.5zm.75 3V2.5h3V4h-3zm-2.874-.467a.75.75 0 00-.752-1.298A1.75 1.75 0 002 3.75v9.5c0 .966.784 1.75 1.75 1.75h8.5A1.75 1.75 0 0014 13.25v-9.5a1.75 1.75 0 00-.874-1.515.75.75 0 10-.752 1.298.25.25 0 01.126.217v9.5a.25.25 0 01-.25.25h-8.5a.25.25 0 01-.25-.25v-9.5a.25.25 0 01.126-.217z"></path>
</svg>
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-check js-clipboard-check-icon color-text-success d-none m-2">
    <path fill-rule="evenodd" d="M13.78 4.22a.75.75 0 010 1.06l-7.25 7.25a.75.75 0 01-1.06 0L2.22 9.28a.75.75 0 011.06-1.06L6 10.94l6.72-6.72a.75.75 0 011.06 0z"></path>
</svg>
    </clipboard-copy>
  </div></div>
<p>Here are profiles I am using. </p>
<details>
  <summary>Click to expand </summary>
<div class="snippet-clipboard-content position-relative"><pre><code>user
├── default
│   ├── files
│   │   └── package
│   │       ├── kernel
│   │       │   └── linux
│   │       │       └── modules
│   │       │           └── netconsole.mk
│   │       ├── openwrt-packages
│   │       │   ├── libJudy
│   │       │   │   ├── Makefile
│   │       │   │   └── patches
│   │       │   │       ├── 100-host-compile-JudyTablesGen.patch
│   │       │   │       └── 300-makefile-nodoc-notest.patch
│   │       │   └── netconsole
│   │       │       ├── Makefile
│   │       │       └── files
│   │       │           ├── netconsole.config
│   │       │           └── netconsole.init
│   │       └── system
│   │           └── fstools
│   │               └── 0010-fstools-block-make-extroot-mount-preparation-more-robust.patch
│   └── patches
│       ├── 000-download-max-time.patch
│       ├── 002-netdata-with-dbengine.patch
│       ├── 003-netdata-init-with-TZ.patch
│       └── 006-autossh-init.patch
├── wdr4310v1
│   ├── config.diff
│   ├── custom.sh
│   ├── files
│   │   └── package
│   │       ├── firmware
│   │       │   └── wireless-regdb
│   │       │       └── patches
│   │       │           └── 601-reghack.patch
│   │       ├── kernel
│   │       │   └── mac80211
│   │       │       └── patches
│   │       │           └── ath
│   │       │               └── 499-ath9k_reghack.patch
│   │       └── network
│   │           └── services
│   │               └── hostapd
│   │                   └── patches
│   │                       └── 399-reghack.patch
│   ├── packages.txt
│   └── settings.ini
└── x86_64
    ├── config.diff
    ├── custom.sh
    ├── packages.txt
    ├── patches
    │   ├── 005-openclash-clashr.patch.disable
    │   └── 007-transmission-init.patch
    └── settings.ini
</code></pre><div class="zeroclipboard-container position-absolute right-0 top-0">
    <clipboard-copy aria-label="Copy" class="ClipboardButton btn js-clipboard-copy m-2 p-0 tooltipped-no-delay" data-copy-feedback="Copied!" data-tooltip-direction="w" value="user
├── default
│   ├── files
│   │   └── package
│   │       ├── kernel
│   │       │   └── linux
│   │       │       └── modules
│   │       │           └── netconsole.mk
│   │       ├── openwrt-packages
│   │       │   ├── libJudy
│   │       │   │   ├── Makefile
│   │       │   │   └── patches
│   │       │   │       ├── 100-host-compile-JudyTablesGen.patch
│   │       │   │       └── 300-makefile-nodoc-notest.patch
│   │       │   └── netconsole
│   │       │       ├── Makefile
│   │       │       └── files
│   │       │           ├── netconsole.config
│   │       │           └── netconsole.init
│   │       └── system
│   │           └── fstools
│   │               └── 0010-fstools-block-make-extroot-mount-preparation-more-robust.patch
│   └── patches
│       ├── 000-download-max-time.patch
│       ├── 002-netdata-with-dbengine.patch
│       ├── 003-netdata-init-with-TZ.patch
│       └── 006-autossh-init.patch
├── wdr4310v1
│   ├── config.diff
│   ├── custom.sh
│   ├── files
│   │   └── package
│   │       ├── firmware
│   │       │   └── wireless-regdb
│   │       │       └── patches
│   │       │           └── 601-reghack.patch
│   │       ├── kernel
│   │       │   └── mac80211
│   │       │       └── patches
│   │       │           └── ath
│   │       │               └── 499-ath9k_reghack.patch
│   │       └── network
│   │           └── services
│   │               └── hostapd
│   │                   └── patches
│   │                       └── 399-reghack.patch
│   ├── packages.txt
│   └── settings.ini
└── x86_64
    ├── config.diff
    ├── custom.sh
    ├── packages.txt
    ├── patches
    │   ├── 005-openclash-clashr.patch.disable
    │   └── 007-transmission-init.patch
    └── settings.ini
" tabindex="0" role="button">
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-clippy js-clipboard-clippy-icon m-2">
    <path fill-rule="evenodd" d="M5.75 1a.75.75 0 00-.75.75v3c0 .414.336.75.75.75h4.5a.75.75 0 00.75-.75v-3a.75.75 0 00-.75-.75h-4.5zm.75 3V2.5h3V4h-3zm-2.874-.467a.75.75 0 00-.752-1.298A1.75 1.75 0 002 3.75v9.5c0 .966.784 1.75 1.75 1.75h8.5A1.75 1.75 0 0014 13.25v-9.5a1.75 1.75 0 00-.874-1.515.75.75 0 10-.752 1.298.25.25 0 01.126.217v9.5a.25.25 0 01-.25.25h-8.5a.25.25 0 01-.25-.25v-9.5a.25.25 0 01.126-.217z"></path>
</svg>
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-check js-clipboard-check-icon color-text-success d-none m-2">
    <path fill-rule="evenodd" d="M13.78 4.22a.75.75 0 010 1.06l-7.25 7.25a.75.75 0 01-1.06 0L2.22 9.28a.75.75 0 011.06-1.06L6 10.94l6.72-6.72a.75.75 0 011.06 0z"></path>
</svg>
    </clipboard-copy>
  </div></div>
</details>
<h4><a id="user-content-default-profile" class="anchor" aria-hidden="true" href="#default-profile"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Default profile </h4>
<p>You can provide default profile in  <code>user/default</code>. The files in  <code>user/${TARGET_NAME}</code> and  <code>user/default</code> will be automatically merged (internally creating a folder named  <code>user/current</code>). When they have conflicted files, generally file-level overriding strategy is used; files in  <code>user/${TARGET_NAME}</code> will overwrite same files in  <code>user/default</code>, with only one exception  <code>settings.ini</code>. </p>
<p>For  <code>settings.ini</code>, option-level instead of file-level overriding is the strategy for merging. For example, for option  <code>REPO_BRANCH</code>, overriding will happen onto the same option in  <code>user/default/settings.ini</code> if it is set in  <code>user/${TARGET_NAME}/settings.ini</code>, but option  <code>REPO_URL</code> won't be affected by this fact (the two options are independent). </p>
<h3><a id="user-content-manually-adding-packages" class="anchor" aria-hidden="true" href="#manually-adding-packages"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Manually adding packages </h3>
<p>Use  <code>user/${TARGET_NAME}/packages.txt</code></p>
<div class="snippet-clipboard-content position-relative"><pre><code>Packages will be put into 'package/openwrt-packages' dir
Note that to have it compiled, you also have to set its CONFIG_* options
The format is:
PACKAGE_NAME PACKAGE_GIT [ref=REF] [root=ROOT] [subdir=SUBDIR] [rename=RENAME] [mkfile-dir=MKFILE_DIR] [use-latest-tag] [override]
REF is optional. You can specify branch/tag/commit
ROOT is optional. Specifying the parent path under 'package/' of this package. Defaults to 'openwrt-packages'.
SUBDIR is optional. The path of subdir within the repo can be specified
RENAME is optional. It allows renaming of PKG_NAME in Makefile of the package
MKFILE_DIR is optional. You can specify the dir of Makefile, only used when RENAME is specified.
'use-latest-tag' will retrieve latest release as the REF. It shouldn't be specified together with REF. Currently only github repo is supported.
'override' will delete packages that are already existed.

Examples:
mentohust https://github.com/KyleRicardo/MentoHUST-OpenWrt-ipk.git
luci-app-mentohust https://github.com/BoringCat/luci-app-mentohust.git ref=1db86057
syslog-ng https://github.com/openwrt/packages.git ref=master root=feeds/packages/admin subdir=admin/syslog-ng override
</code></pre><div class="zeroclipboard-container position-absolute right-0 top-0">
    <clipboard-copy aria-label="Copy" class="ClipboardButton btn js-clipboard-copy m-2 p-0 tooltipped-no-delay" data-copy-feedback="Copied!" data-tooltip-direction="w" value="Packages will be put into 'package/openwrt-packages' dir
Note that to have it compiled, you also have to set its CONFIG_* options
The format is:
PACKAGE_NAME PACKAGE_GIT [ref=REF] [root=ROOT] [subdir=SUBDIR] [rename=RENAME] [mkfile-dir=MKFILE_DIR] [use-latest-tag] [override]
REF is optional. You can specify branch/tag/commit
ROOT is optional. Specifying the parent path under 'package/' of this package. Defaults to 'openwrt-packages'.
SUBDIR is optional. The path of subdir within the repo can be specified
RENAME is optional. It allows renaming of PKG_NAME in Makefile of the package
MKFILE_DIR is optional. You can specify the dir of Makefile, only used when RENAME is specified.
'use-latest-tag' will retrieve latest release as the REF. It shouldn't be specified together with REF. Currently only github repo is supported.
'override' will delete packages that are already existed.

Examples:
mentohust https://github.com/KyleRicardo/MentoHUST-OpenWrt-ipk.git
luci-app-mentohust https://github.com/BoringCat/luci-app-mentohust.git ref=1db86057
syslog-ng https://github.com/openwrt/packages.git ref=master root=feeds/packages/admin subdir=admin/syslog-ng override
" tabindex="0" role="button">
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-clippy js-clipboard-clippy-icon m-2">
    <path fill-rule="evenodd" d="M5.75 1a.75.75 0 00-.75.75v3c0 .414.336.75.75.75h4.5a.75.75 0 00.75-.75v-3a.75.75 0 00-.75-.75h-4.5zm.75 3V2.5h3V4h-3zm-2.874-.467a.75.75 0 00-.752-1.298A1.75 1.75 0 002 3.75v9.5c0 .966.784 1.75 1.75 1.75h8.5A1.75 1.75 0 0014 13.25v-9.5a1.75 1.75 0 00-.874-1.515.75.75 0 10-.752 1.298.25.25 0 01.126.217v9.5a.25.25 0 01-.25.25h-8.5a.25.25 0 01-.25-.25v-9.5a.25.25 0 01.126-.217z"></path>
</svg>
      <svg aria-hidden="true" viewBox="0 0 16 16" version="1.1" data-view-component="true" height="16" width="16" class="octicon octicon-check js-clipboard-check-icon color-text-success d-none m-2">
    <path fill-rule="evenodd" d="M13.78 4.22a.75.75 0 010 1.06l-7.25 7.25a.75.75 0 01-1.06 0L2.22 9.28a.75.75 0 011.06-1.06L6 10.94l6.72-6.72a.75.75 0 011.06 0z"></path>
</svg>
    </clipboard-copy>
  </div></div>
<h2><a id="user-content-details" class="anchor" aria-hidden="true" href="#details"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Details </h2>
<h3><a id="user-content-building-process-explained" class="anchor" aria-hidden="true" href="#building-process-explained"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Building process explained </h3>
<blockquote>
<p>Note:  <code>user/current/*</code> files are files merged from  <code>user/${TARGET_NAME}/*</code> and  <code>user/default/*</code>. </p>
</blockquote>
<h4><a id="user-content-first-time-building" class="anchor" aria-hidden="true" href="#first-time-building"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>First-time building </h4>
<ol>
<li><code>update_repo.sh</code>. Download OpenWrt source code. </li>
<li><code>update_feeds.sh</code>. Install all packages from feeds and manually added ones defined in  <code>user/current/packages.txt</code>. </li>
<li><code>customize.sh</code>. Copy  <code>user/current/.config</code> files, copy  <code>user/current/files</code>, apply patches in  <code>user/current/patches</code>, Execute  <code>user/current/custom.sh</code>. </li>
<li><code>download.sh</code>, Execute  <code>make download</code></li>
<li><code>compile.sh</code>, Multi/single-thread compile </li>
<li>Upload current builder both as  <code>${BUILDER_NAME}:${BUILDER_TAG}</code> and  <code>${BUILDER_NAME}:${BUILDER_TAG}-inc</code> to docker registry. </li>
<li>Upload files to Artifacts
 <ul>
<li><code>OpenWrt_packages</code>: all packages </li>
<li><code>OpenWrt_firmware</code>: firmware </li>
</ul>
</li>
</ol>
<h4><a id="user-content-following-buildings" class="anchor" aria-hidden="true" href="#following-buildings"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Following buildings </h4>
<ol>
<li>Pull  <code>${BUILDER_NAME}:${BUILDER_TAG}-inc</code> (if  <code>rebase</code> is enabled, use  <code>${BUILDER_NAME}:${BUILDER_TAG}</code> instead) from docker registry. </li>
<li><code>update_repo.sh</code>. Only when  <code>update_repo</code> option is set, update OpenWrt source code. </li>
<li><code>update_feeds.sh</code>. Install missing packages in  <code>user/current/packages.txt</code>. Only when  <code>update_feeds</code> option is set, update all feed packages and manually added packages. </li>
<li><code>customize.sh</code>. Copy  <code>user/current/.config</code> files, copy  <code>user/current/files</code>, apply patches in  <code>user/current/patches</code>, Execute  <code>user/current/custom.sh</code>. </li>
<li><code>download.sh</code>, Execute <code>make download</code></li>
<li><code>compile.sh</code>, Multi/single-thread compile</li>
<li>Upload current builder as <code>${BUILDER_NAME}:${BUILDER_TAG}-inc</code> to docker registry (overwrite old one).</li>
<li>Upload files to Artifacts
<ul>
<li><code>OpenWrt_packages</code>: all packages</li>
<li><code>OpenWrt_firmware</code>: firmware only</li>
</ul>
</li>
</ol>
<h3><a id="user-content-squashing-strategy" class="anchor" aria-hidden="true" href="#squashing-strategy"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Squashing Strategy</h3>
<p>Squashing is used for compressing your builders. When too many layers are stacked in a docker image, it usually has a large amount of duplicated and unused files wasting space. Squashing will compress all docker layers into single one to improve space efficiency. This is automatically executed when building if the number of layers exceeds a threshold. However it takes extra time to execute. You can enable periodic and manual squashing job by uncommenting trigger events in  <code>.github/workflows/squash.yml</code>. </p>
<h2><a id="user-content-debugging-and-manually-configuring" class="anchor" aria-hidden="true" href="#debugging-and-manually-configuring"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Debugging and manually configuring </h2>
<p>Thanks to  <a href="https://tmate.io/" rel="nofollow">tmate </a>, you can enter into both the docker containers and GitHub Actions runners through SSH to debug and manually change your configurations, e.g.  <code>make menuconfig</code>. To do it, you have to enable the building option:  <code>debug</code>. See  <a href="#building-options">Building Options </a> for ways of using options. </p>
<p><a target="_blank" rel="noopener noreferrer" href="/tete1030/openwrt-fastbuild-actions/blob/master/docs/imgs/debug.png"><img src="/tete1030/openwrt-fastbuild-actions/raw/master/docs/imgs/debug.png" alt="SSH" style="max-width:100%;"></a></p>
<p>For safety of your sensitive information, you  <strong>must </strong> either set  <code>SLACK_WEBHOOK_URL</code> or  <code>TMATE_ENCRYPT_PASSWORD</code> in the  <strong>Secrets </strong> page to protect the tmate connection info. Refer to  <a href="https://github.com/tete1030/safe-debugger-action/blob/master/README.md">tete1030/safe-debugger-action/README.md </a> for details. </p>
<p>Note that the configuration changes you made should only be for  <strong>temporary usage </strong>. Even though your changes in the docker container will be saved to Docker Hub, there are situations where you manual configuration may lost: </p>
<ol>
<li>The  <code>rebuild</code> or  <code>rebase</code> option is set </li>
<li>Some files will be overwritten during every building. For example, if you have executed  <code>make menuconfig</code> in the container, the changes of the  <code>.config</code> file seem saved. In fact, during next building, the  <code>config.diff</code> file will be copied to overwrite the old  <code>.config</code>. </li>
</ol>
<p>To make permanent changes, it is always recommended to use the  <code>user/${TARGET_NAME}</code> mechanism. </p>
<h3><a id="user-content-important-directories" class="anchor" aria-hidden="true" href="#important-directories"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Important directories </h3>
<p>Here list the directories (inside docker containers) that are used internally when building </p>
<ul>
<li><code>${OPENWRT_CUR_DIR}</code>: OpenWrt directory that is currently working on (equal to either  <code>${OPENWRT_COMPILE_DIR}</code> or  <code>${OPENWRT_SOURCE_DIR}</code>) </li>
<li><code>${OPENWRT_COMPILE_DIR}</code>: OpenWrt directory used for compiling (normally in docker container's  <code>/home/builder/openwrt</code>) </li>
<li><code>${OPENWRT_SOURCE_DIR}</code>: OpenWrt directory used for creating source code tree (normally in docker container's  <code>/tmp/builder/openwrt</code>) </li>
</ul>
<p>When debugging, in most cases you should use  <code>${OPENWRT_CUR_DIR}</code> as this is the dir that the builder is currently working on and where error happened. </p>
<h2><a id="user-content-faqs" class="anchor" aria-hidden="true" href="#faqs"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>FAQs </h2>
<h3><a id="user-content-why-all-targets-seem-triggered-when-only-some-are-intended" class="anchor" aria-hidden="true" href="#why-all-targets-seem-triggered-when-only-some-are-intended"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Why all targets seem triggered when only some are intended? </h3>
<p>This is expected. We use a script instead of workflow conditions to select which jobs should be executed. This means every target would be launched to run the selection script. Targets that are not intended will quit quickly. This method allows better complexity for the selection process. </p>
<h2><a id="user-content-todo" class="anchor" aria-hidden="true" href="#todo"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Everything </h2>
<p>See <a href="/tete1030/openwrt-fastbuild-actions/blob/master/TODO">ALL </a></p>
<h2><a id="user-content-acknowledgments" class="anchor" aria-hidden="true" href="#acknowledgments"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>Acknowledgments </h2>
<ul>
<li><a href="https://github.com/P3TERX/Actions-OpenWrt">P3TERX's Actions-Openwrt </a></li>
<li><a href="https://github.com/crazy-max/ghaction-docker-buildx">crazy-max/ghaction-docker-buildx </a></li>
<li><a href="https://hub.docker.com/" rel="nofollow">Docker Hub </a></li>
<li><a href="https://azure.microsoft.com" rel="nofollow">Microsoft Azure </a></li>
<li><a href="https://github.com/features/actions">GitHub Actions </a></li>
<li><a href="https://github.com/tmate-io/tmate">tmate </a></li>
<li><a href="https://github.com/mxschmitt/action-tmate">mxschmitt/action-tmate </a></li>
<li><a href="https://github.com/csexton/debugger-action">csexton/debugger-action </a></li>
<li><a href="https://github.com/openwrt/openwrt">OpenWrt </a></li>
<li><a href="https://github.com/coolsnowwolf/lede">Lean's OpenWrt </a></li>
</ul>
<h2><a id="user-content-license" class="anchor" aria-hidden="true" href="#license"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M7.775 3.275a.75.75 0 001.06 1.06l1.25-1.25a2 2 0 112.83 2.83l-2.5 2.5a2 2 0 01-2.83 0 .75.75 0 00-1.06 1.06 3.5 3.5 0 004.95 0l2.5-2.5a3.5 3.5 0 00-4.95-4.95l-1.25 1.25zm-4.69 9.64a2 2 0 010-2.83l2.5-2.5a2 2 0 012.83 0 .75.75 0 001.06-1.06 3.5 3.5 0 00-4.95 0l-2.5 2.5a3.5 3.5 0 004.95 4.95l1.25-1.25a.75.75 0 00-1.06-1.06l-1.25 1.25a2 2 0 01-2.83 0z"></path></svg></a>License </h2>
<p>Most files under </p>
<p><a href="/tete1030/openwrt-fastbuild-actions/blob/master/LICENSE">WITH </a>© Texot </p>
<p>Original idea and some files under </p>
<p><a href="https://github.com/P3TERX/Actions-OpenWrt/blob/master/LICENSE">WITH </a>© P3TERX </p>
</article>
