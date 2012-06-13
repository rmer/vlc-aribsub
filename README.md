vlc-aribsub
===========

日本のデジタル放送のMPEG-TSの字幕表示に対応したVLCです。

はじめに
--------
日本のデジタル放送（地デジ・BSデジタル）には字幕が含まれているものがあります。
その字幕情報をどうにかしてVLCで直接表示する方法がないかと、
[ARIB](http://www.arib.or.jp/)の規格書を読んだり他のプログラムのソースを読んだりしつつ、
軽い気持ちで作ったものです。

本家VLCとの差違
---------------
* 番組情報表示  
    メディア情報画面(`ツール→コーデック`)のコーデック画面に表示される局名やEPG  
    プログラム情報（`再生→プログラム`）に表示される局名  
    を正しくデコードして文字化けしないように表示します。
* 字幕出力  
    `ビデオ→字幕トラック`のところの、「ARIB字幕」「ARIB subtitles」を選択したときに、
    ビデオ画面上に字幕を表示します。

上記機能（番組情報表示・字幕出力）は、
`設定→すべて→入力／コーデック→デマルチプレクサー→MPEG-TS`
の「Support ARIB STD-B24」というところのチェックで機能のオンオフができます。デフォルトはオフ。

設定項目
--------
* 使用フォント指定  
    `設定→字幕とOSD`のところで指定します。ARIB外字や絵文字が表示できるものを使うことを推奨します。  
    例、和田研中丸ゴシック2004絵文字
* 字幕の自動選択  
    `設定→字幕とOSD`の「優先する字幕の言語」に、jpnもしくはjaを指定します。
    自動選択をしたい場合のみ設定してください。
    この設定をした場合、ARIB字幕のみならず他の字幕フォーマットでも適用される可能性があります。
* ARIB字幕（ARIB Subtitles）モジュールの設定  
    `設定→すべて→入力／コーデック→字幕コーデック→ARIB字幕(ARIB Subtitles)`  
    和田研中丸ゴシック2004絵文字を使用時には、以下の設定をいじる必要は無いと思います。
    * 「ルビ（ふりがな）抑制」「Ignore ruby(furigana)」  
        ルビ（ふりがな）の出力をしたくない場合には、この項目にチェックを入れます。
    * 「位置調整抑制」「Ignore position adjustment」  
        Freetype2フォントレンダラー使用時に、特定の文字（句読点、括弧、「ゃ」「っ」等）
        が表示される位置がずれる場合があり、その際には調整を行う必要があります。
        この調整を行わなくていい場合には、この項目にチェックを入れます。
        コアテキストフォントレンダラーを使用する場合には、この調整は必要が無いため、
        チェックを入れないと駄目かと思われます。
    * 「エリプシス（...）置換」「Replace horizontal ellipsis」  
        フォントによっては"…"三点リーダーが"..."のように下付で出てしまう場合があります。
        U+2026 HORIZONTAL ELLIPSISで通常は中点が三つでるような感じになる筈ですが、そうでないフォントもあるため、
        U+22EF MIDLINE HORIZONTAL ELLIPSISに置き換えて表示するための設定です。
    * 「フォントサイズ調整比」「Font size adjustment ratio」  
        Freetype2フォントレンダラーやコアテキスフォントトレンダラーともに、
        文字間のスペースを調整することが現状出来ないため、
        その分の調整を明示的にフォントサイズを調整することにより行うための設定です。
        デフォルト値は、0.5です。
        数値を増減することにより、フォントのサイズが一定の比率で増減します。
        0.0はサイズを変更しないことを意味します。
    * 「半角出力時フォントサイズ調整比」「Halfwidth adjustment ratio」  
        幅を半分にして表示する文字を出力する際に、フォントによって半分にした際の幅が大きい場合があり、
        その調整を行うための設定です。
        デフォルト値は、-1.0です。
        数値を増減することにより、フォントのサイズが一定の比率で増減します。
        0.0はサイズを変更しないことを意味します。
    * 「水平方向調整値」「Horizontal adjustment value」  
        字幕を表示する位置を水平方向にどれだけずらすかの設定です。
        デフォルト値は、0です。
        正の値を指定した場合、通常よりも右方にずれて表示され、
        負の値を指定した場合、通常よりも左方にずれて表示されます。
    * 「垂直方向調整値」「Vertical adjustment value」  
        字幕を表示する位置を垂直方向にどれだけずらすかの設定です。
        デフォルト値は、0です。
        正の値を指定した場合、通常よりも下方にずれて表示され、
        負の値を指定した場合、通常よりも上方にずれて表示されます。
* 外字置換  
    DRCS外字を置換するための、外字置換設定ファイル（drcs_conv.ini）を所定のディレクトリに置きます。

    Windows `%APPDATA%\vlc\arib` (%APPDATA%はC:\Users\XXX\AppData)  
    Mac OS X `~/Library/Application Support/org.videolan.vlc/arib`  
    Linux `~/.local/share/vlc/arib`
    
    外字置換設定ファイルのフォーマットは、  
    ```ini
    md5のハッシュ値=Unicodeのコードポイント値
    ```
    のようになります。
    
    例えば、携帯電話を表すDRCS外字（ハッシュ値68fc649b4a57a6103a25dc678fcec9f4）を
    Unicodeの'MOBILE PHONE' U+1F4F1で置き換えたい場合、
    ```ini
    68fc649b4a57a6103a25dc678fcec9f4=U+1f4f1
    ```
    のように設定します。
    使用するするフォントに含まれているものを指定してください。
    上記コード(U+1F4F1)は恐らくサポートしているフォントは少ないと思われます。
    和田研中丸ゴシック2004絵文字にはその手のフォントは沢山含まれています。
    
    ハッシュ値をどのように把握するかについては、設定ファイルを置くディレクトリ
    のサブディレクトリdata (Windowsだと、%APPDATA%\vlc\arib\data)の下に、
    ハッシュ値.pngというファイル名で自動的に外字のイメージが作成されるように
    なっていますので、そこから判断してください。

Mac OS X版特有の設定項目
------------------------
Mac OS X版には、字幕やOSD表示するためのテキストレンダラーとして、
デフォルトで使われるfreetype以外にもquartztext（コアテキスト）というものも
用意されています。
今回、コアテキストフォントレンダラーをある程度使えるように改造してあります。
コアテキストフォントレンダラーを使うメリットとしては、
指定したフォントに含まれていない文字を出力しようとしたときに別のフォントから文字を探してくれたり、
テキストレイアウトが改善したりすることが上げられます。

* テキストレンダラーの選択  
    `設定→すべて→ビデオ→字幕とOSD`の「テキストレンダリングモジュール」で

    * デフォルト（Freetype2フォントレンダラー）
    * コアテキストフォントレンダラー
    
    のどちらかを選択することが出来ます。
    個人的には、コアテキストフォントレンダラーをお勧めします。
* 使用フォント指定  
    コアテキストフォントレンダラーを選択した場合には、使用フォント設定は、
    `設定→すべて→ビデオ→字幕とOSD→Mac用のテキストレンダラー`で指定します。
    この設定は、基本設定のものとは異なるため、別途指定する必要があります。
    指定する内容は、基本設定と同じで問題無いので、基本設定で選びその値を
    コピーすれば良いと思います。
    例えば、ヒラギノ角ゴシック ProN W6を使用する場合には、
    「HiraKakuProN-W6」「Hiragino Kaku Gothic ProN W6」
    あたりを指定します。
* ARIB字幕（ARIB Subtitles）モジュールの設定   
    `設定→すべて→入力／コーデック→字幕コーデック→ARIB字幕(ARIB Subtitles)`  
    設定項目のところに設定内容は書きましたが、コアテキストフォントレンダラーでヒラギノ使用時には
    以下のように指定することをお勧めします。
    * 「位置調整抑制」「Ignore position adjustment」  
        この設定にはチェックを入れます。
    * 「フォントサイズ調整比」「Font size adjustment ratio」  
        1.0を指定します。
    * 「半角出力時フォントサイズ調整比」「Halfwidth adjustment ratio」  
        0.0を指定します。
    * 「水平方向調整値」「Horizontal adjustment value」  
        -12を指定します。
    * 「垂直方向調整値」「Vertical adjustment value」  
        12を指定します。

コアテキストフォントレンダラーでヒラギノ使用時に面白いのは、外字設定ファイルで絵文字がでるように指定した場合、
グラフィカルな絵文字をカラー絵文字フォント(Apple Emoji Font)から出力してくれることです。
携帯(U+1F4F1)の場合、iPhoneみたいなのがでたり、TV(U+1F4FA)やチャイム(U+1F4E2)もちゃんとしたものが出るようになります。