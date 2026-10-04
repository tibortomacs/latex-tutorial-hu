# TeXstudio szkriptek

[https://tomacstibor.uni-eszterhazy.hu/latex.html](https://tomacstibor.uni-eszterhazy.hu/latex.html)

Normál ékezetes betűket repülő ékezetes betűkre konvertálja az egész dokumentumban. 
(`"g"` a global szóra utal, azaz az egész dokumentumban cserél. 
A `\` jel `\\` módon, a `"` jel pedig `\"` módon írható be.)
```java
editor.replace("ö","g","\\\"{o}");
editor.replace("ü","g","\\\"{u}");
editor.replace("ó","g","\\'{o}");
editor.replace("ő","g","\\H{o}");
editor.replace("ú","g","\\'{u}");
editor.replace("é","g","\\'{e}");
editor.replace("á","g","\\'{a}");
editor.replace("ű","g","\\H{u}");
editor.replace("í","g","\\'{i}");
editor.replace("Ö","g","\\\"{O}");
editor.replace("Ü","g","\\\"{U}");
editor.replace("Ó","g","\\'{O}");
editor.replace("Ő","g","\\H{O}");
editor.replace("Ú","g","\\'{U}");
editor.replace("É","g","\\'{E}");
editor.replace("Á","g","\\'{A}");
editor.replace("Ű","g","\\H{U}");
editor.replace("Í","g","\\'{I}");
```

Repülő ékezetes betűket normál ékezetes betűkre konvertálja az egész dokumentumban.
```java
editor.replace("\\\"{o}","g","ö");
editor.replace("\\\"o","g","ö");
editor.replace("\\\"{u}","g","ü");
editor.replace("\\\"u","g","ü");
editor.replace("\\'{o}","g","ó");
editor.replace("\\'o","g","ó");
editor.replace("\\H{o}","g","ő");
editor.replace("\\H o","g","ő");
editor.replace("\\'{u}","g","ú");
editor.replace("\\'u","g","ú");
editor.replace("\\'{e}","g","é");
editor.replace("\\'e","g","é");
editor.replace("\\'{a}","g","á");
editor.replace("\\'a","g","á");
editor.replace("\\H{u}","g","ű");
editor.replace("\\H u","g","ű");
editor.replace("\\'{i}","g","í");
editor.replace("\\'i","g","í");
editor.replace("\\'{\\i}","g","í");
editor.replace("\\'\\i","g","í");
editor.replace("\\\"{O}","g","Ö");
editor.replace("\\\"O","g","Ö");
editor.replace("\\\"{U}","g","Ü");
editor.replace("\\\"U","g","Ü");
editor.replace("\\'{O}","g","Ó");
editor.replace("\\'O","g","Ó");
editor.replace("\\H{O}","g","Ő");
editor.replace("\\H O","g","Ő");
editor.replace("\\'{U}","g","Ú");
editor.replace("\\'U","g","Ú");
editor.replace("\\'{E}","g","É");
editor.replace("\\'E","g","É");
editor.replace("\\'{A}","g","Á");
editor.replace("\\'A","g","Á");
editor.replace("\\H{U}","g","Ű");
editor.replace("\\H U","g","Ű");
editor.replace("\\'{I}","g","Í");
editor.replace("\\'I","g","Í");
```

A kijelölt szöveget `\url` parancsba teszi.
```java
selected = cursor.selectedText();
editor.insertText("\\url{" + selected + "}");
if (selected == "") cursor.moveTo(cursor.lineNumber(),cursor.columnNumber()-1);
```

A kijelölt szöveg betűit nagybetűre konvertálja.
```java
cursor.insertText(cursor.selectedText().toUpperCase());
```

A kijelölt szöveg betűit kisbetűre konvertálja.
```java
cursor.insertText(cursor.selectedText().toLowerCase());
```

Sortörés ki/bekapcsolása. 
Az utolsó állapotot a TeXstudio megjegyzi, és az újraindításnál aszerint működik.
```java
if (getPersistent("Editor/WordWrapMode") == "1"){
editor.setLineWrapping(false);
setPersistent("Editor/WordWrapMode","0")
} else {
editor.setLineWrapping(true);
setPersistent("Editor/WordWrapMode","1")
}
editor.setSoftLimitedLineWrapping(false);
editor.setHardLineWrapping(false);
```

Külső program indítása.
```java
system("cmd /C call \"C:/program/program.exe\"","")
```

Külső program indítása. 
A szkript futása megáll addig, amíg a külső program fut. 
Ezután a vágólap tartalma beíródik az editorba.
```java
var prog = system("cmd /C call \"[txs-app-dir]/program.exe\"");
prog.waitForFinished();
cursor.insertText(app.clipboard);
```

Külső program futtatása, amely kiír valamit a konzolra (stdout). 
Ezt a szöveget beírja a dokumentumunkba is.
```java
var prog = system("cmd /C call \"[txs-app-dir]/program.exe\"");
prog.standardOutputRead.connect(function(output){editor.write(output);});
prog.waitForFinished();
```

A kijelölt sorok elé `\item` parancsokat tesz, és az egészet `itemize` környezetbe rakja.
```java
selected = cursor.selectedText(); 
items = new Array();
if (selected) {
items = selected.split("\n");
n = items.length;
}
else {
items[0] = ""; 
n = 1;
}
editor.insertText("\\begin{itemize}\n");
for (i = 0; i < n; i++) {
editor.insertText("\\item "+items[i]+"\n");
}
editor.insertText("\\end{itemize}\n");
cursor.moveTo(cursor.lineNumber()-2,6);
cursor.removeSelectedText(); 
```

Írjon a TeXstudióba képfájlokat, sortöréssel elválasztva azokat, majd jelölje ki őket. 
Futtatva a szkriptet, minden képfájlt beír figure környezetbe. 
Ha nincs kijelölés, akkor párbeszédablakban ki lehet választani egy fájlt.
```java
selected = cursor.selectedText(); 
filenames = new Array();
if (selected) {
filenames = selected.split("\n");
n = filenames.length;
}
else {
fileChooser.exec(); 
pth = fileChooser.fileName(); 
filenames[0] = pth.replace(/^.*\//, ''); 
n = 1;
}
for (i = 0; i < n; i++) {
editor.insertText("\\begin{figure}[!h]\n\\centering\n\\includegraphics[width=5cm]{"+filenames[i]+"}\n\\caption{}\n\\label{fig-"+filenames[i]+"}\n\\end{figure}\n");
}
cursor.moveTo(cursor.lineNumber()-3,10);
cursor.removeSelectedText(); 
```

Adott sor és oszlopszámú `array` környezet készítése.
```java
dialog = new UniversalInputDialog()
dialog.add("3", "Rows:", "m")
dialog.add("3","Columns:","n")
if (dialog.exec() != ""){ 
t = "";
s = "";
for (j = 1; j <= dialog.get("n"); j++){s += "c"}
for (i = 1; i <= dialog.get("m"); i++)
{
for (j = 1; j <= dialog.get("n"); j++)
{
if (j < dialog.get("n")){t += i+""+j+" & "}else{t += i+""+j};
}
t += "\\\\ \n";
}
t = "\\begin{array}{"+s+"}\n" + t + "\\end{array}";
editor.insertText(t)}
```

Kijelölt szóból környezetet készít.
```java
env = cursor.selectedText();
if (env != ""){
cursor.insertText("\\begin{"+env+"}\n\n\\end{"+env+"}");
cursor.removeSelectedText();
cursor.moveTo(cursor.lineNumber()-1,0); 
} else {warning("Először jelöljön ki egy szót!")};
```

Kijelölt részt `columns` környezetbe rakja.
```java
editor.insertText("\\begin{columns}\n\\column{5.5cm}\n"+cursor.selectedText()+"\n\\column{5.5cm}\n\n\\end{columns}");
cursor.moveTo(cursor.lineNumber()-1,0);
cursor.removeSelectedText(); 
```

Kijelölt részt listából kiválasztható környezetbe teszi.
```java
dialog = new UniversalInputDialog()
dialog.add(["lemma","definition","theorem","proof","remark"],"Environment:","env")
dialog.setWindowTitle("Insert environment")
if (dialog.exec() != ""){ 
editor.insertText("\\begin{"+dialog.get("env")+"}\n"+cursor.selectedText()+"\n\\end{"+dialog.get("env")+"}");
cursor.moveTo(cursor.lineNumber()-1,0);
cursor.removeSelectedText()} 
```

Kijelölt részt adott környezetbe teszi. 
Az utoljára megadott környezetnévre emlékszik még a Texstudio leállítása után is, mert azt kiírja a `texstudio.ini` fájlba.
```java
if (hasPersistent("UserEnvironment") == false){setPersistent("UserEnvironment","")};
text = getPersistent("UserEnvironment");
dialog = new UniversalInputDialog()
dialog.add(text,"Environment:","env");
dialog.setWindowTitle("Insert environment");
if (dialog.exec() != ""){ 
editor.insertText("\\begin{"+dialog.get("env")+"}\n"+cursor.selectedText()+"\n\\end{"+dialog.get("env")+"}");
cursor.moveTo(cursor.lineNumber()-1,0);
cursor.removeSelectedText();
setPersistent("UserEnvironment",dialog.get("env"))}
```

Kijelölt részt listából kiválasztható színnel szedi ki.
```java
dialog = new UniversalInputDialog()
dialog.add(["red","blue","green","yellow","gray"],"Color:","color")
dialog.setWindowTitle("Insert color")
if (dialog.exec() != ""){ 
if (cursor.selectedText() != ""){
editor.insertText("{\\color{"+dialog.get("color")+"}"+cursor.selectedText()+"}");
cursor.removeSelectedText()} 
else {
editor.insertText("\\color{"+dialog.get("color")+"}")}}
```

A `geometry` csomag használatát segítő szkript.
```java
dialog = new UniversalInputDialog()
dialog.add(["\\usepackage[options]{geometry}","\\geometry{options}","\\newgeometry{options}"],"Insert:","ins")
dialog.add("","magnification =","a")
dialog.add("","paper width =","b")
dialog.add("","paper height =","c")
dialog.add("","text width =","d")
dialog.add("","text height =","e")
dialog.add("","inner margin =","f")
dialog.add("","outer margin =","g")
dialog.add("","top margin =","h")
dialog.add("","bottom margin=","i")
dialog.add("","head height =","j")
dialog.add("","head sep =","k")
dialog.add("","foot skip =","l")
dialog.add("","marginpar width =","m")
dialog.add("","marginpar sep =","n")
dialog.setWindowTitle("Package geometry");
if (dialog.exec() != ""){
opt = "";
if (dialog.get("ins") == "\\usepackage[options]{geometry}"){before = "\\usepackage["; after = "]{geometry}"};
if (dialog.get("ins") == "\\geometry{options}"){before = "\\geometry{"; after = "}"};
if (dialog.get("ins") == "\\newgeometry{options}"){before = "\\newgeometry{"; after = "}\n\n\\restoregeometry"};
if (dialog.get("a") != ""){opt = "mag=" + dialog.get("a")};
if (dialog.get("b") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "paperwidth=" + dialog.get("b")};
if (dialog.get("c") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "paperheight=" + dialog.get("c")};
if (dialog.get("d") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "textwidth=" + dialog.get("d")};
if (dialog.get("e") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "textheight=" + dialog.get("e")};
if (dialog.get("f") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "inner=" + dialog.get("f")};
if (dialog.get("g") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "outer=" + dialog.get("g")};
if (dialog.get("h") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "top=" + dialog.get("h")};
if (dialog.get("i") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "bottom=" + dialog.get("i")};
if (dialog.get("j") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "headheight=" + dialog.get("j")};
if (dialog.get("k") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "headsep=" + dialog.get("k")};
if (dialog.get("l") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "footskip=" + dialog.get("l")};
if (dialog.get("m") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "marginparwidth=" + dialog.get("m")};
if (dialog.get("n") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "marginparsep=" + dialog.get("n")};
if (opt != ""){editor.insertText(before + opt + after)};
}
```

A `hyperref` csomag használatát segítő szkript.
```java
dialog = new UniversalInputDialog()
dialog.add(["\\usepackage[options]{hyperref}","\\hypersetup{options}"],"Insert:","ins")
dialog.add(["","0","1","2","3","4","5","6","7",],"bookmarksopenlevel","c")
dialog.add(["default","Fit","FitH","FitV"],"pdfstartview","i")
dialog.add("","linkcolor","j")
dialog.add("","citecolor","k")
dialog.add("","pagecolor","l")
dialog.add("","filecolor","m")
dialog.add("","urlcolor","n")
dialog.add("","allcolors","o")
dialog.add(true,"bookmarks","a")
dialog.add(false,"bookmarksopen","b")
dialog.add(false,"bookmarksnumbered","d")
dialog.add(false,"linktocpage","e")
dialog.add(false,"breaklinks","f")
dialog.add(false,"colorlinks","g")
dialog.add(false,"pdfpagemode=FullScreen","h")
dialog.add(true,"linkframe","p")
dialog.add(true,"hyperfootnotes","q")
dialog.setWindowTitle("Package hyperref");
if (dialog.exec() != ""){
	opt = "";
	if (dialog.get("ins") == "\\usepackage[options]{hyperref}"){before = "\\usepackage"; after = "{hyperref}"}
		else {before = "\\hypersetup{"; after = "}"};
	if (dialog.get("a") == false){opt = "bookmarks=false"};
	if (dialog.get("b") == true){if (opt != ""){opt = opt + ","}; opt = opt + "bookmarksopen"};
	if (dialog.get("c") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "bookmarksopenlevel=" + dialog.get("c")};
	if (dialog.get("d") == true){if (opt != ""){opt = opt + ","}; opt = opt + "bookmarksnumbered"};
	if (dialog.get("e") == true){if (opt != ""){opt = opt + ","}; opt = opt + "linktocpage"};
	if (dialog.get("f") == true){if (opt != ""){opt = opt + ","}; opt = opt + "breaklinks"};
	if (dialog.get("g") == true){if (opt != ""){opt = opt + ","}; opt = opt + "colorlinks"};
	if (dialog.get("h") == true){if (opt != ""){opt = opt + ","}; opt = opt + "pdfpagemode=FullScreen"};
	if (dialog.get("i") != "default"){if (opt != ""){opt = opt + ","}; opt = opt + "pdfstartview=" + dialog.get("i")};
	if (dialog.get("j") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "linkcolor=" + dialog.get("j")};
	if (dialog.get("k") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "citecolor=" + dialog.get("k")};
	if (dialog.get("l") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "pagecolor=" + dialog.get("l")};
	if (dialog.get("m") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "filecolor=" + dialog.get("m")};
	if (dialog.get("n") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "urlcolor=" + dialog.get("n")};
	if (dialog.get("o") != ""){if (opt != ""){opt = opt + ","}; opt = opt + "allcolors=" + dialog.get("o")};
	if (dialog.get("p") == false){if (opt != ""){opt = opt + ","}; opt = opt + "pdfborder={0 0 0}"};
	if (dialog.get("q") == false){if (opt != ""){opt = opt + ","}; opt = opt + "hyperfootnotes=false"};
	if (dialog.get("ins") == "\\hypersetup{options}"){if (opt != ""){editor.insertText(before + opt + after)}}
		else {if (opt != ""){editor.insertText(before + "[" + opt + "]" + after)} else {editor.insertText(before + after)}}
}
``` 

A kijelölt részt listából kiválasztható betűtípussal szedi ki. 
Ha nem jelölünk ki szöveget, akkor csak a deklarációs parancsot szúrja be.
```java
dialog = new UniversalInputDialog()
dialog.add(["normalfont","upshape","slshape","itshape","em","scshape","mdseries","bfseries","rmfamily","sffamily","ttfamily"],"Font Style Switches:","com")
dialog.setWindowTitle("Font Style Switches")
if (dialog.exec() != ""){ 
if (cursor.selectedText() != ""){
editor.insertText("{\\"+dialog.get("com")+" "+cursor.selectedText()+"}");
cursor.removeSelectedText()} 
else {
editor.insertText("\\"+dialog.get("com")+" ")}}
```

A kijelölt részt listából kiválasztható betűmérettel szedi ki. 
Ha nem jelölünk ki szöveget, akkor csak a deklarációs parancsot szúrja be.
```java
dialog = new UniversalInputDialog()
dialog.add(["tiny","scriptsize","footnotesize","small","normalsize","large","Large","LARGE","huge","Huge"],"Font Size Switches:","com")
dialog.setWindowTitle("Font Size Switches")
if (dialog.exec() != ""){ 
if (cursor.selectedText() != ""){
editor.insertText("{\\"+dialog.get("com")+" "+cursor.selectedText()+"}");
cursor.removeSelectedText()} 
else {
editor.insertText("\\"+dialog.get("com")+" ")}}
```

A kijelölt részt összevonja egy sorba.
```java
var mytext=cursor.selectedText();
var txt=mytext.replace(/\n/g," ");
txt=txt.replace(/	/g," ");
txt=txt.replace(/    /g," ");
txt=txt.replace(/   /g," ");
txt=txt.replace(/  /g," ");
cursor.replaceSelectedText(txt);
```

<!-- Copy-gomb: CSS -->
<style>
pre { position: relative; overflow: auto; }
.copy-btn {
  position: absolute;
  top: 6px;
  right: 6px;
  border: none;
  background: rgba(0,0,0,0.6);
  color: white;
  padding: 6px;
  border-radius: 4px;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  font-size: 12px;
}
.copy-btn:focus { outline: 2px solid #fff; }
.copy-btn .icon { width: 16px; height: 16px; display:inline-block; vertical-align:middle; }
.copy-btn.copied { background: rgba(0,128,0,0.8); }
</style>

<!-- Copy-gomb: JS -->
<script>
(function(){
  const svg = '<svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round" xmlns="http://www.w3.org/2000/svg"><rect x="9" y="9" width="13" height="13" rx="2"/><path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/></svg>';
  function makeButton() {
    const btn = document.createElement('button');
    btn.className = 'copy-btn';
    btn.type = 'button';
    btn.title = 'Szöveg másolása';
    btn.setAttribute('aria-label','Szöveg másolása');
    btn.innerHTML = svg;
    return btn;
  }

  function getCodeText(pre) {
    const code = pre.querySelector('code');
    return (code ? code.innerText : pre.innerText).replace(/\u00A0/g,' ');
  }

  function installButtons() {
    document.querySelectorAll('pre').forEach(pre => {
      if (pre.querySelector('.copy-btn')) return;
      const text = getCodeText(pre).trim();
      if (!text) return;
      pre.style.position = pre.style.position || 'relative';
      const btn = makeButton();
      pre.appendChild(btn);

      btn.addEventListener('click', async (e) => {
        e.preventDefault();
        const txt = getCodeText(pre);
        try {
          await navigator.clipboard.writeText(txt);
          btn.classList.add('copied');
          btn.title = 'Másolva';
          btn.innerHTML = '✓';
          setTimeout(()=>{ btn.classList.remove('copied'); btn.innerHTML = svg; btn.title='Szöveg másolása'; }, 1800);
        } catch (err) {
          const ta = document.createElement('textarea');
          ta.value = txt;
          document.body.appendChild(ta);
          ta.select();
          try { document.execCommand('copy'); 
            btn.classList.add('copied');
            btn.innerHTML = '✓';
            setTimeout(()=>{ btn.classList.remove('copied'); btn.innerHTML = svg; }, 1800);
          } catch(e2) {
            alert('Másolás sikertelen — jelöld ki és másold manuálisan.');
          }
          document.body.removeChild(ta);
        }
      });
    });
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', installButtons);
  } else {
    installButtons();
  }
  const observer = new MutationObserver(installButtons);
  observer.observe(document.body, { childList: true, subtree: true });
})();
</script>