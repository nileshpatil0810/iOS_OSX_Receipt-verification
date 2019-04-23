# iOS_OSX_Receipt-verification
How to verify iOS/OSX Receipt?

Ref link : https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html

$receipt_data = str_replace('-', '+', $receipt_data);
$receipt_data = str_replace(' ', '+', $receipt_data);
$receipt_data = str_replace('_', '/', $receipt_data);
$sand_box = $sand_box != '' ? $sand_box : '0';
$sand_box_url = SANDBOX_RECEIPT_URL;
$apple_live_url = LIVE_RECEIPT_URL;
$receipt_password = iTUNES_ACCOUNT_PASSWORD;

$receipt_body = json_encode(array("receipt-data" => $receipt_data, "password" => $receipt_password));
if ($sand_box == '1') {
    $reciept_url = $sand_box_url;
} else {
    $reciept_url = $apple_live_url;
}

//POST function
$receipt_response = _post_curl($receipt_body, $reciept_url);
$response = json_decode($receipt_response);
$status = $response->status;
if ($status == '21007') { //Verify again with sandbox URL
    $receipt_response = _post_curl($receipt_body, $sand_box_url);
    $response = json_decode($receipt_response);
    return $response;
} else if ($status == '21008') { //Verify again with LIVE URL
    $receipt_response = _post_curl($receipt_body, $apple_live_url);
    $response = json_decode($receipt_response);
    return $response;
} else {
    return $response;
}
