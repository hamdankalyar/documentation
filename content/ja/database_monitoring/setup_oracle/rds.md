---
description: RDS Oracle のデータベースモニタリングをインストールして構成する
further_reading:
- link: /integrations/oracle/
  tag: Documentation
  text: Basic Oracle インテグレーション
is_beta: true
kind: documentation
private: true
title: RDS Oracle のデータベースモニタリングの設定
---

{{< site-region region="gov" >}}
データベースモニタリングはこのサイトでサポートされていません。</div>
{{< /site-region >}}

<div class="alert alert-info">
このページで説明されている機能は非公開ベータ版です。
</div>

データベースモニタリングは、クエリサンプルを公開することで、Oracle データベースを深く可視化し、さまざまなワークロードをプロファイリングして問題を診断します。

データベースでデータベースモニタリングを有効にするには、以下の手順を実行します。

1. [Agent 用のデータベースユーザーのセットアップ](#agent-database-user-setup)。
2. [Agent のインストール][1]。

## Agent 用のデータベースユーザーのセットアップ

Datadog Agent がサンプルを収集するためには、データベース サーバーへの読み取り専用のアクセスが必要となります。

### ユーザーの作成

レガシー Oracle インテグレーションをインストールした場合は、ユーザーがすでに存在するので、この手順をスキップしてください。ただし、以降レガシーのステップは実行する必要があります。

サーバーに接続するための読み取り専用ログインを作成し、必要な権限を付与します。

```SQL
CREATE USER datadog IDENTIFIED BY your_password ;
```

### 権限の付与

```SQL
grant create session to datadog ;
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$SESSION','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$DATABASE','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$CONTAINERS','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$SQLSTATS','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$SQL','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$INSTANCE','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$SQL_PLAN_STATISTICS_ALL','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_FEATURE_USAGE_STATISTICS','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$PROCESS','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$SESSION','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$CON_SYSMETRIC','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('CDB_TABLESPACE_USAGE_METRICS','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('CDB_TABLESPACES','DATADOG','SELECT',p_grant_option => false); 
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$SQLCOMMAND','DATADOG','SELECT',p_grant_option => false);
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$DATAFILE','DATADOG','SELECT',p_grant_option => false);
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$SGAINFO','DATADOG','SELECT',p_grant_option => false);
exec rdsadmin.rdsadmin_util.grant_sys_object('V_$SYSMETRIC','DATADOG','SELECT',p_grant_option => false);
```

## Agent の構成

Oracle テレメトリーの収集を開始するには、まず [Datadog Agent をインストール][1]します。

Oracle Agent のコンフィギュレーションファイル `/etc/datadog-agent/conf.d/oracle-dbm.d/conf.yaml` を作成します。使用可能なすべての構成オプションは、[サンプルコンフィギュレーションファイル][2]を参照してください。

```yaml
init_config:
instances:
  - server: '<RDS_INSTANCE_ENDPOINT_1>:<PORT>'
    service_name: "<SERVICE_NAME>" # Oracle CDB サービス名
    username: 'c##datadog'
    password: '<PASSWORD>'
    dbm: true
    tags:  # Optional
      - 'service:<CUSTOM_SERVICE>'
      - 'env:<CUSTOM_ENV>'
  - server: '<RDS_INSTANCE_ENDPOINT_2>:<PORT>'
    service_name: "<SERVICE_NAME>" # Oracle CDB サービス名
    username: 'c##datadog'
    password: '<PASSWORD>'
    dbm: true
    tags:  # Optional
      - 'service:<CUSTOM_SERVICE>'
      - 'env:<CUSTOM_ENV>'
```

`service` と `env` タグを使用して、共通のタグ付けスキームでデータベースのテレメトリーを他のテレメトリーにリンクします。これらのタグが Datadog でどのように使用されるかについてさらに詳しくは、[統合サービスタグ付け][3]を参照してください。

すべての Agent の構成が完了したら、[Datadog Agent を再起動][4]します。

### 検証

[Agent の status サブコマンドを実行][5]し、**Checks** セクションで `oracle-dbm` を探します。Datadog の[ダッシュボード][7]と[データベース][6]のページへ移動して開始します。

[1]: /ja/database_monitoring/setup_oracle/#install-agent
[2]: https://github.com/DataDog/datadog-agent/blob/main/cmd/agent/dist/conf.d/oracle-dbm.d/conf.yaml.example
[3]: /ja/getting_started/tagging/unified_service_tagging
[4]: /ja/agent/guide/agent-commands/#start-stop-and-restart-the-agent
[5]: /ja/agent/guide/agent-commands/#agent-status-and-information
[6]: https://app.datadoghq.com/databases
[7]: https://app.datadoghq.com/dash/integration/30990/dbm-oracle-database-overview

## その他の参考資料

{{< partial name="whats-next/whats-next.html" >}}