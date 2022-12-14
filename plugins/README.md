## Direktori Plugins
Berkas `plugins.go` adalah register untuk package plugins yang ada dibawahnya. Menghapus berkas ini dapat mengakibatkan bot tidak akan berjalan.

## Cara membuat plugin
### Contoh listing pembuatan direktori :
```sh
plugins # direktori dasar plugins
└── downloader 
    ├── download.go # nama package <nama> harus sama
    └── converter.go # nama package <nama> harus sama
```

### Contoh isi `download.go` :

<details>
<summary>Lihat</summary>

```go
package downloader // harus sama dengan nama direktori

import (
  "log"
  "main/core/types"
  "main/core/whats"
  "main/core/validators"
  "main/plugins"
  "log"

  waProto "go.mau.fi/whatsmeow/binary/proto"
  "google.golang.org/protobuf/proto"

  "go.mau.fi/whatsmeow"
  "go.mau.fi/whatsmeow/types/events"
)

var PluginDownloader = plugins.Add("App Downloader", plugValidator)

// Contoh validator
func plugValidator(i interface{}, client *whatsmeow.Client) (bool, error) {

  // nilai return validator 
  // bool : jika true maka result pada error akan dicetak
  // error : jika nilai error adalah nil, maka akses diterima/valid
  // sebaliknya jika error bukan nil, maka command yang ada di dalam
  // PluginDownloader tidak akan dieksekusi

  return false, error
}


func init() {
  PluginDownloader.SetDisable() // jika ingin menonaktifkannya

  PluginDownloader.CommandAddMany([]*types.Command{
    {
      Cmd:         []string{".wget", ".wg"},
      Description: "Download file",
      Usage:       "{cmd} url",
      Execute:     commandWget,

      // Jika di set true command tidak akan di eksekusi
      Disable: false,

      // Jika di set true maka tidak akan diperiksa kecocokan command
      // Langsung di ekseskusi tanpa dicek dengan Cmd yang ada diatas
      Passed: false,
    },
  })
}

// contoh isi parameter yang akan diterima
// pattern : .wget
// args : ["url-1", "url-2"]
// cmd : object Command (self)
// event : event pesan
// client : Whatsapp client instance

// nilai return adalah error

func commandWget(pattern string, args []string, cmd *types.Command, event *events.Message, ctx *waProto.ContextInfo, client *whatsmeow.Client) error {

  var msg = &waProto.Message{}
  var resp, err = whats.SendMessage(event.Info.Chat, msg, client)
    
  log.Println(resp)

  return err
}
```

</details>


### Contoh isi `converter.go` :
```go
package downloader

import (
    "fmt"
)

func init() {

    // Dapat menambahkan command lain dengan variable Plugin yang sama
    PluginDownloader.CommandAddMany([]*types.Command{
        {
            ...
        },
    })

}

```


## Keterangan
Metode pengelolaan plugins masih jauh dari kata aman. Silahkan berkontribusi untuk membangun sistem ini menjadi lebih baik.