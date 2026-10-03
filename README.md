# Mikuryu

> Custom WhatsApp Web API dengan dukungan full interactive button support.

Fork of [Baileys](https://github.com/WhiskeySockets/Baileys) by WhiskeySockets.

Maintained by [@jafrisebat20-sketch](https://github.com/jafrisebat20-sketch).

## Install

```bash
npm install mikuryu
```

## Quick Start

```javascript
import makeWASocket, {
  useMultiFileAuthState,
  fetchLatestBaileysVersion
} from 'mikuryu'

import P from 'pino'

const logger = P({ level: 'silent' })

const { state } = await useMultiFileAuthState('./session')
const { version } = await fetchLatestBaileysVersion()

const sock = makeWASocket({
  version,
  logger,
  auth: state
})

sock.ev.on('messages.upsert', async ({ messages }) => {
  for (const msg of messages) {
    const text = msg.message?.conversation
    if (text === '!menu') {
      await sock.sendMessage(msg.key.remoteJid, {
        interactiveMessage: {
          body: { text: 'Pilih menu:' },
          footer: { text: 'Powered by Mikuryu' },
          nativeFlowMessage: {
            buttons: [
              {
                name: 'quick_reply',
                buttonParamsJson: JSON.stringify({
                  display_text: 'Main',
                  id: 'play'
                })
              },
              {
                name: 'quick_reply',
                buttonParamsJson: JSON.stringify({
                  display_text: 'Statistik',
                  id: 'stats'
                })
              },
              {
                name: 'cta_url',
                buttonParamsJson: JSON.stringify({
                  display_text: 'Website',
                  url: 'https://example.com',
                  merchant_url: 'https://example.com'
                })
              },
              {
                name: 'single_select',
                buttonParamsJson: JSON.stringify({
                  title: 'Pilih Kategori',
                  sections: [{
                    title: 'Kategori',
                    rows: [
                      { id: 'cat_a', title: 'Kategori A', description: 'deskripsi a' },
                      { id: 'cat_b', title: 'Kategori B', description: 'deskripsi b' }
                    ]
                  }]
                })
              }
            ],
            messageParamsJson: ''
          }
        }
      })
    }
  }
})
```

## Supported Button Types

Semua tipe button interaktif WhatsApp didukung:

| Button | Deskripsi |
|--------|-----------|
| `quick_reply` | Tombol balas cepat |
| `cta_url` | Buka link eksternal |
| `cta_call` | Telepon nomor |
| `cta_copy` | Copy teks ke clipboard |
| `single_select` | Dropdown pilihan tunggal |
| `multi_select` | Dropdown pilihan banyak |
| `cta_datetime` | Pemilih tanggal/waktu |
| `cta_catalog` | Katalog bisnis |
| `send_location` | Bagikan lokasi |

## Example — Send Buttons Helper

```javascript
const NF = {
  quickReply: (id, text) => ({
    name: 'quick_reply',
    buttonParamsJson: JSON.stringify({ display_text: text, id })
  }),
  ctaUrl: (text, url) => ({
    name: 'cta_url',
    buttonParamsJson: JSON.stringify({ display_text: text, url, merchant_url: url })
  }),
  ctaCall: (text, phone) => ({
    name: 'cta_call',
    buttonParamsJson: JSON.stringify({ display_text: text, phone_number: phone })
  }),
  ctaCopy: (text, code) => ({
    name: 'cta_copy',
    buttonParamsJson: JSON.stringify({ display_text: text, copy_code: code })
  }),
  singleSelect: (title, sections) => ({
    name: 'single_select',
    buttonParamsJson: JSON.stringify({ title, sections })
  }),
  multiSelect: (title, sections) => ({
    name: 'multi_select',
    buttonParamsJson: JSON.stringify({ title, sections })
  })
}

// Usage
await sock.sendMessage(jid, {
  interactiveMessage: {
    body: { text: 'Pilih menu:' },
    nativeFlowMessage: {
      buttons: [
        NF.quickReply('play', 'Main'),
        NF.ctaUrl('Website', 'https://example.com'),
        NF.ctaCall('Telepon', '+6281234567890'),
        NF.ctaCopy('Copy Token', 'ABC-123'),
        NF.singleSelect('Kategori', [{
          title: 'Pilih',
          rows: [
            { id: 'a', title: 'Opsi A' },
            { id: 'b', title: 'Opsi B' }
          ]
        }])
      ],
      messageParamsJson: ''
    }
  }
})
```

## Versioning

Current version: `7.0.0-rc16`

Based on Baileys `7.0.0-rc14`.

## License

MIT — based on [Baileys](https://github.com/WhiskeySockets/Baileys) by WhiskeySockets.
