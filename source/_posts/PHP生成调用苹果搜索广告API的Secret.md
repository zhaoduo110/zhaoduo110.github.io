---
title: PHP生成调用苹果搜索广告API的Secret
categories:
  - - PHP
tags:
  - PHP
abbrlink: 8864ca16
date: 2021-10-25 14:38:13
top_img: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/U10131P115DT20150107140651.jpg
cover: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/U10131P115DT20150107140651.jpg
---



公司运营需求需要从苹果搜索广告后台拉取对应的广告投放数据，在对接API苹果OAUTH2.0 API获取AccessToken时生成的secret（JWT）时遇到了一些问题

其中使用了 `lcobucci/jwt` 和 `firebase/php-jwt` 的库，但是不行，应该是使用姿势不正确，还是要表示感谢

最后多亏 [Apple Sign In “invalid_client”, sign JWT for authe - 码农岛 (manongdao.com)](https://www.manongdao.com/article-1836532.html) 中的解答才解决了问题，特此记录



苹果API文档：https://developer.apple.com/documentation/apple_search_ads/implementing_oauth_for_the_apple_search_ads_api?changes=latest_major

按照文档说明一步步操作即可，最终拿到clientId、teamId和keyId用于生成JWT Token



### PHP生成JWT TOKEN的方法

GenerateAppleApiJwtToken.php

```php
<?php

/**
 * Class GenerateAppleApiJwtToken
 */
class GenerateAppleApiJwtToken
{

    static $clientId;
    static $teamId;
    static $keyId;
    static $privateKeyFile;
    static $exp=3600;
    static $audience='https://appleid.apple.com';
    static $alg= 'ES256';

    /**
     * GenerateAppleApiJwtToken constructor.
     * @param string $keyId
     * @param string $teamId
     * @param string $clientId
     * @param int $exp
     */
    public function __construct($keyId='', $teamId='', $clientId='', $exp=0)
    {
        if (!empty($keyId)) {
            self::$keyId = $keyId;
        }
        if (!empty($teamId)) {
            self::$teamId = $teamId;
        }
        if (!empty($clientId)) {
            self::$clientId = $clientId;
        }
        if ($exp != 0) {
            self::$exp = $exp;
        }
    }

    /**
     * @param string $kid
     * @param string $iss
     * @param string $sub
     * @param string $privateKeyFile
     * @param int $exp
     * @return bool|string
     */
    public static function generateJWT($kid='', $iss='', $sub='', $privateKeyFile='', $exp=0) {

        $keyId = empty($kid) ? self::$keyId : $kid;
        $teamId = empty($iss) ? self::$teamId : $iss;
        $clientId = empty($sub) ? self::$clientId : $sub;
        $privateKeyFile = empty($privateKeyFile) ? self::$privateKeyFile : $privateKeyFile;
        $exp = $exp==0 ? self::$exp : $exp;

        if (empty($keyId)) {
            throw new InvalidArgumentException('Please input argument key id - '.$keyId);
        }
        if (empty($teamId)) {
            throw new InvalidArgumentException('Please input argument iss - '.$teamId);
        }
        if (empty($clientId)) {
            throw new InvalidArgumentException('Please input argument sub - '.$clientId);
        }
        if (empty($privateKeyFile)) {
            throw new InvalidArgumentException('Please input argument private file - '.$privateKeyFile);
        }

        $header = [
            'alg' => self::$alg,
            'kid' => $keyId
        ];
        $body = [
            'iss' => $teamId,
            'sub' => $clientId,
            'iat' => time(),
            'exp' => time() + $exp,
            'aud' => self::$audience,
        ];

        $privateKey = openssl_pkey_get_private(file_get_contents($privateKeyFile));
        if (!$privateKey){
            return false;
        }

        $payload = self::encode(json_encode($header)).'.'.self::encode(json_encode($body));

        $signature = '';
        $success = openssl_sign($payload, $signature, $privateKey, OPENSSL_ALGO_SHA256);
        if (!$success) return false;

        $raw_signature = self::fromDER($signature, 64);

        return $payload.'.'.self::encode($raw_signature);
    }

    /**
     * @param $data
     * @return string
     */
    private static function encode($data) {
        $encoded = strtr(base64_encode($data), '+/', '-_');
        return rtrim($encoded, '=');
    }

    /**
     * @param string $der
     * @param int $partLength
     * @return false|string
     */
    private static function fromDER(string $der, int $partLength)
    {
        $hex = unpack('H*', $der)[1];
        if ('30' !== mb_substr($hex, 0, 2, '8bit')) { // SEQUENCE
            throw new \RuntimeException();
        }
        if ('81' === mb_substr($hex, 2, 2, '8bit')) { // LENGTH > 128
            $hex = mb_substr($hex, 6, null, '8bit');
        } else {
            $hex = mb_substr($hex, 4, null, '8bit');
        }
        if ('02' !== mb_substr($hex, 0, 2, '8bit')) { // INTEGER
            throw new \RuntimeException();
        }
        $Rl = hexdec(mb_substr($hex, 2, 2, '8bit'));
        $R = self::retrievePositiveInteger(mb_substr($hex, 4, $Rl * 2, '8bit'));
        $R = str_pad($R, $partLength, '0', STR_PAD_LEFT);
        $hex = mb_substr($hex, 4 + $Rl * 2, null, '8bit');
        if ('02' !== mb_substr($hex, 0, 2, '8bit')) { // INTEGER
            throw new \RuntimeException();
        }
        $Sl = hexdec(mb_substr($hex, 2, 2, '8bit'));
        $S = self::retrievePositiveInteger(mb_substr($hex, 4, $Sl * 2, '8bit'));
        $S = str_pad($S, $partLength, '0', STR_PAD_LEFT);
        return pack('H*', $R.$S);
    }

    /**
     * @param string $data
     * @return string
     */
    private static function retrievePositiveInteger(string $data)
    {
        while ('00' === mb_substr($data, 0, 2, '8bit') && mb_substr($data, 2, 2, '8bit') > '7f') {
            $data = mb_substr($data, 2, null, '8bit');
        }
        return $data;
    }
}
```

demo.php

```php
<?php
require_once './GenerateAppleApiJwtToken.php';


function http_post_json($json,$t_url)
{
    $postData = http_build_query($json);
    $curl = curl_init();
    curl_setopt($curl, CURLOPT_URL, $t_url);
    curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false); // stop verifying certificate
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($curl, CURLOPT_POST, true);
    curl_setopt($curl, CURLOPT_HTTPHEADER, array('Content-Type: application/x-www-form-urlencoded'));
    curl_setopt($curl, CURLOPT_POSTFIELDS, $postData);
    curl_setopt($curl, CURLOPT_FOLLOWLOCATION, true);
    $r = curl_exec($curl);
    curl_close($curl);
    return $r;
}

$client_id = 'SEARCHADS.xxxxxxxxxxxxxxxxxxxxx';
$team_id = 'SEARCHADS.xxxxxxxxxxxxxxxxxxxx';
$key_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx';
/**
 * 生成JWT
 */
$generateAppleApiJwtToken = new GenerateAppleApiJwtToken();
$token = $generateAppleApiJwtToken::generateJWT($key_id, $team_id, $client_id, './private-key.pem');
var_dump($token);

/**
 * 获取Access Token
 */
$postData=[
    'grant_type'=>'client_credentials',
    'scope'=>'searchadsorg',
    'client_id'=>$client_id,
    'client_secret'=>$token,
];
$b  = http_post_json($postData, 'https://appleid.apple.com/auth/oauth2/token');
var_dump($b);
```









