# Compact Log Event Format (CLEF)

A simple, modern, JSON-based format for structured application logs. CLEF places minimal requirements on the schema of application log events, but specifies enough to enable useful interoperability between tools.

## Schema

Each event is a single JSON object. Any JSON property on the payload object is assumed to be a regular property of the event, apart from the reified properties below.

The format defines a handful of reified properties that have special meaning:

| Property | Name | Description | Required? |
| -------- | ---- | ----------- | --------- |
| `@t`     | Timestamp | An ISO 8601 timestamp | Yes |
| `@m`     | Message | A fully-rendered message describing the event | |
| `@mt` | Message template | Alternative to Message; specifies a [message template](https://messagetemplates.org) over the event's properties that provides for rendering into a textual description of the event | |
| `@l` | Level | An implementation-specific level or severity identifier (string or number) | Absence implies "informational"  |
| `@x` | Exception | A language-dependent error representation potentially including backtrace | |
| `@i` | Event id | An implementation specific event id (string or number) | |
| `@r` | Renderings | If `@mt` includes tokens with programming-language-specific formatting, an array of pre-rendered values for each such token | May be omitted; if present, the count of renderings must match the count of formatted tokens exactly |

The `@` sigil may be escaped at the start of a user property name by doubling, e.g. `@@name` denotes a property called `@name`.

### Example event

A simple `Hello, {User}` event that occured on June 7th, 2016:

```json
{"@t":"2016-06-07T03:44:57.8532799Z","@mt":"Hello, {User}","User":"nblumhardt"}
```

## Streams

When multiple events are written to a text file or stream:

 * Every line must contain a valid event adhering to the CLEF schema, and
 * Every line must end with `\n`, `\r\n`, or the end of the file/stream.

Encoding is not specified, but in the absence of a clearly designated encoding, UTF-8 should be assumed.

## Versioning

Versioning of the CLEF format is additive only, with no version identifier; implementations should treat any unrecognised reified properties as if they are user data.

## Tools

Various tools are available for working with the CLEF format.

 * **[_Analogy.LogViewer.Serilog_](https://github.com/Analogy-LogViewer/Analogy.LogViewer.Serilog)** - CLEF parser for [Analogy Log Viewer](https://github.com/Analogy-LogViewer/Analogy.LogViewer)
 * **[`clef-tool`](https://github.com/datalust/clef-tool)** - a CLI application for processing CLEF files
 * **[Compact Log Format Viewer](https://github.com/warrenbuckley/Compact-Log-Format-Viewer)** - a cross-platform viewer for CLEF files
 * **[`seqcli`](https://github.com/datalust/seqcli)** - pretty-`print` CLEF files at the command-line, or `ingest` CLEF files into [Seq](https://datalust.co/seq) for search, and analysis
 * **[_Serilog.Formatting.Compact.Reader_](https://github.com/serilog/serilog-formatting-compact-reader)** - convert CLEF documents back into Serilog `LogEvent`s
 
