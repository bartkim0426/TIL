## Use bigqury in vim

- `bq` command 사용
  - https://cloud.google.com/bigquery/docs/bq-command-line-tool
  - need `~/.bigqueryrc`

- execute `bq query` command from the file: bq query `cat filepath`, `bq query --flagfile=/path/to/file.sql`, `bq query < myfile.sql`
  - https://stackoverflow.com/questions/33426395/google-bigquery-bq-command-line-execute-query-from-a-file
- [use bigquery in vim](use-bigquery-in-vim)

- `*.sql` file -> trigger -> show result (or log) in other buffer (?)


### 사전작업

- writing vim plugins blog article: http://stevelosh.com/blog/2011/09/writing-vim-plugins/
- Learn vimscrip the hard way: http://learnvimscriptthehardway.stevelosh.com/
- 

- pipe output to new buffer
  - `:new | r ! find /tmp -name '*.txt'`
- get vim filename
  - `expand('%:p')`
  - 

- `:vnew | r ! bq query < filepath`

### vim에서 new tab으로 열기
`:tabnew | r ! bq query `cat luigi/bigquery_local/mgmt.sql``

- [o] filename -> 직접 안 적고 사용 가능하게 하면 좋을듯...
  - [X] tabnew, vnew 등을 사용하면 filename을 `expand('%:p')`에서 가져올 수 없음
  - [.] registry에서 쓰면? `:vnew | put =system('bq query', getreg('@n'))` query가 바로 argument로 사용되어서 사용 못함...
	- [X] registry 에 `cat filename` 형태로 저장한 후 그걸 reg에 저장하면 어떨지? -> 안됨 ㅠㅠ
    - [ ] previous buffer name 사용? `expand('#2:p')`
    - [ ] :vnew | r ! bq query < expand('#2:p')

### buffer -> tab으로 전환
- vim expand to tab: `tab sb 2`

