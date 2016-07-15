layout: '[layout]'
title: 分页数据类
date: 2016-03-11 20:55:05
tags: [php]
categories: [php]
---
### 分页数据类

```php
<?php
class Page{
   private $total;                       //表中记录总数
   private $listRows;                //每页显示行数
   private $limit="limit 0,10";   
   private $uri;
   private $pageNum;              //总页数
   private $config=array('header'=>"条记录","prev"=>"上一页","next"=>"下一页","first"=>"首页","last"=>"尾页");

   //初始化参数
   public function __construct($total,$listRows=10){
      $this->total=$total;
      $this->listRows=$listRows;
      $this->uri=$this->getUri();

      //判断GET["PAGE"]是否为空   并为page赋值 page显示的当前页数
      $this->page=!empty($_GET["page"]) ? $_GET["page"] : 1;
      //获取总页数  ceil取整
      $this->pageNum=ceil($this->total/$this->listRows);
      $this->limit=$this->setLimit();
   }
   private function setLimit(){
      //$result = "LIMIT ".($this->page-1)*($this->listRows).",{$this->listRows}";
        $result = array(($this->page-1)*($this->listRows),$this->listRows);
        return $result;
   }

   //获取url
   private function getUri(){
      //获取当前uri  并判断uri里面是否有?号
      $url=$_SERVER["REQUEST_URI"].(strpos($_SERVER["REQUEST_URI"],'?')?'':"?");
      $parse=parse_url($url);
      //判断$parse["query"]是否存在
      if(isset($parse["query"])){
         //解析$parse["query"]转为数组  注：相同参数配置 只取最后提交的一个 
         parse_str($parse["query"],$params);
         //删除参数pagea
         unset($params["page"]);
         //当前路径+新组合的url
         $url=$parse["path"].'?'.http_build_query($params).'&';
      }
      return $url;
   }

   //外部可访问$limit属性
   public function __get($args){
      if($args=="limit"){
         return $this->limit;
      }else{
         return null;
      }
   }
   //表下方显示记录信息
   public function fpage(){
      if($this->pageNum<=1){
         return '';
      }
      $html="";
      $html.="<ul class='pagination'><li><span style=\"background-color: #fff;\">显示 ".(($this->page-1)*($this->listRows)+1)."-".(($this->page)*($this->listRows))." 条 共{$this->total}条 ".$this->page."/".$this->pageNum."页</span></li>";
      //$html.=" 本页显示<b>".($this->enter()-$this->start()+1)."</b>{$this->config["header"]}";
      //$html.="<b>{$this->start()}-{$this->enter()}</b>";
      //$html.="  当前<b>{$this->page}</b>页 共<b>{$this->pageNum}</b>页";
      //第一页
      $html.=$this->first();
      //上一页
      $html.=$this->prev();
      //列表页
      $html.=$this->pageList(3);
      //下一页
      $html.=$this->next();
      //尾页
      $html.=$this->last().'</ul>';
      //跳到第N页
      //$html.=$this->goPage();
      //$html.="</font>";
      return $html;
   }
   private function first(){
      if($this->page==1){
         return $html=" <li><a href={$this->uri}page=1><span aria-hidden=\"false\">{$this->config["first"]}</span></a></li>";
      }else{
         return $html=" <li><a href={$this->uri}page=1><span aria-hidden=\"true\">{$this->config["first"]}</span></a></li>";
      }
   }
   private function prev(){
      if($this->page==1){
         return $html=" <li><a href={$this->uri}page=1> {$this->config["prev"]} </a></li>";;
      }else{
         $page=$this->page-1;
         return $html=" <li><a href={$this->uri}page=$page> {$this->config["prev"]} </a></li>";
      }
   }
   private function pageList($num = 4){
      $linkPage="";
      for($i=$this->page-$num;$i<$this->page+$num+1;$i++){
         if($i<=$this->pageNum && $i>=1){
                if($i == $this->page){
                    $linkPage.="<li ><a class='page_hover' href={$this->uri}page={$i}>$i</a> </li>";
                }else{
                    $linkPage.="<li><a href={$this->uri}page={$i}>$i</a> </li>";
                }

         }
      }
      return $linkPage;
   }
   private function next(){
      if($this->page==$this->pageNum){
         return $html=" <li><a href={$this->uri}page={$this->page}>{$this->config["next"]}</a></li>";
      }else{
         $page=$this->page+1;
         return $html=" <li><a href={$this->uri}page=$page>{$this->config["next"]}</a></li>";
      }
   }
   private function last(){
      if($this->page==$this->pageNum){
         return $html=" <li><a href={$this->uri}page={$this->pageNum}>{$this->config["last"]}</a></li>";
      }else{
         return $html=" <li><a href={$this->uri}page={$this->pageNum}>{$this->config["last"]}</a></li>";
      }
   }
   private function goPage(){
      return ' <input type=text onkeydown="javascript:if(event.keyCode==13){var page=(this.value>'.$this->pageNum.')?'.$this->pageNum.':this.value;location=\''.$this->uri.'page=\'+page+\'\'}" value="'.$this->page.'" style="width=25px"><input type="button" onclick="javascript:{var page=(this.previousSibling.value>'.$this->pageNum.')?'.$this->pageNum.':this.previousSibling.value;location=\''.$this->uri.'page=\'+page+\'\'}" value="go"> ';
   }
   //获取每页记录从那条开始
   private function start(){
      //当总条数为0时返回0
      if($this->total==0){
         return 0;
      }else{
         //返回当前页数-1，乘以每页显示条数在加上1
         return ($this->page-1)*($this->listRows)+1;
      }
   }
   //获取每页记录结束位置
   private function enter(){
      //在当前页数乘以每页条数  与 总记录条数 取最小值返回
      return min($this->page*$this->listRows,$this->total);
   }

    /**
     *
     * @param String $url
     * @param 后缀 $end_fix
     * @param 每页每页显示的条数 pagenums
     * @param 总页数 Integer $totalPages
     * @param 当前页 Integer $currentPage
     * @param 最大显示页 Integer $maxPage
     * @param 附加 [optional] string $descriptions
     * @param 附加一 [optional] string $desp1
     * @param 附加二 [optional] string $desp2
     * @param 附加三  如果=no 不显示文本框 [optional] string $desp3
     * @return string
     *
     */
    public static function getSubContent($url, $end_fix, $totalPages, $currentPage, $maxPage, $total=false, $descriptions = '', $desp1 = '', $desp2 = '', $desp3 = FALSE)
    {
        $temp_str = "";
        $lastPage = $currentPage - 1;
        $nextPage = $currentPage + 1;
        $page_href = "<a href=\"" . $url;
        $descriptions = ' ' . $descriptions . ' ';
        $index_fix_str = $final_fix_str = $last_fix_str = $next_fix_str = '';
        if (!empty($desp1) || !empty($desp2)) {
            $index_fix_str = $desp1 . "1" . $desp2;
            $final_fix_str = $desp1 . $totalPages . $desp2;
            $last_fix_str = $desp1 . $lastPage . $desp2;
            $next_fix_str = $desp1 . $nextPage . $desp2;
        }
        $firstPage ='<li>'. $page_href . "1" . $end_fix . "\"" . $descriptions . $index_fix_str . " target='_self' rel='nofollow'>首页</a></li>";
        //$firstPage='<a href="/">首页</a>';
        $finalPage ='<li>'. $page_href . $totalPages . $end_fix . "\"" . $descriptions . $final_fix_str . " rel='nofollow'>尾页</a></li>";
        $allPage = "<li style='font-family:微软雅黑;font-size:14px;'><a href='javascript:;'>&nbsp;共" . $totalPages . "页</a></li>";
        $alltotals = $total!==false ? "<li style='font-family:微软雅黑;font-size:14px;'><a href='javascript:;'>&nbsp;共" . $total . "条</a></li>" : '';
        if ($lastPage > 0) {
            $temp_str ='<li>'. $page_href . $lastPage . $end_fix . "\"" . $descriptions . $last_fix_str . " class=\"pre\" rel='nofollow'><</a></li>";
        }
        $temp_str .= self::getHref($page_href, $end_fix, $totalPages, $currentPage, $maxPage, '', '', $descriptions, $desp1, $desp2);
        if ($nextPage <= $totalPages) {
            $temp_str .='<li>'. $page_href . $nextPage . $end_fix . "\"" . $descriptions . $next_fix_str . " class=\"next\" rel='nofollow'>></a></li>";
        }
        $input_str = "&nbsp;到第<input type=\"text\" size=\"3\" onkeydown=\"javascript:if(event.keyCode==13) return false;\" style=\"text-align:center;margin:0px 3px;border:1px solid #999;\" id=\"goPageText\" value=\"" . $currentPage . "\">页&nbsp;<input id=\"goPageButton\" onclick=\"javascript:var goP = document.getElementById('goPageText'); if(isNaN(goP.value)||goP.value<1||goP.value>" . $totalPages . "){alert('请正确输入页数!');goP.select();return false;}  window.location.href='" . $url . "'+goP.value+'" . $end_fix . "';\" type=\"button\" value=\"确定\">";
        if ($desp3 == 'no') {
            $input_str = '';
        }
        return'<ul class="pagination">'. $alltotals . $firstPage . $temp_str . $finalPage . $allPage . $input_str.'</ul>';
    }

    private static function getHref($page_href, $end_fix, $totalPages, $currentPage, $maxPage, $title = '', $target = '', $descriptions = '', $desp1, $desp2)
    {
        $href_str = "";
        $midPage = ceil($maxPage / 2);
        if ($totalPages <= $maxPage) { //如果总页数小于要显示的最多页数
            $href_str .= self::getHrefStr($end_fix, 1, $totalPages, $page_href, $currentPage, $descriptions, $desp1, $desp2);
        } else {
            if ($currentPage > $midPage) {
                $toEndPage = $currentPage + $midPage;
                $toStartPage = $currentPage - $midPage;
                if ($toEndPage <= $totalPages) {
                    $href_str .= self::getHrefStr($end_fix, $toStartPage, $toEndPage, $page_href, $currentPage, $descriptions, $desp1, $desp2);
                } else {
                    $href_str .= self::getHrefStr($end_fix, $totalPages - $maxPage + 1, $totalPages, $page_href, $currentPage, $descriptions, $desp1, $desp2);
                }
            } else {
                $href_str .= self::getHrefStr($end_fix, 1, $maxPage, $page_href, $currentPage, $descriptions, $desp1, $desp2);
            }
        }
        return $href_str;
    }

    private static function getHrefStr($end_fix, $k, $totalPages, $page_href, $currentPage, $descriptions, $desp1, $desp2)
    {
        $href_str = '';
        for ($i = $k; $i <= $totalPages; $i++) {
            $page_fix_str = '';
            if ($i == $currentPage || ($i==1 && $i ==! $currentPage)) {
                $href_str .= '<li  class="active">'.$page_href . $i . $end_fix . "\" " . $descriptions . $page_fix_str . ">$i</a></li>";
                continue;
            }

            if (!empty($desp1) || !empty($desp2)) {
                $page_fix_str = $desp1 . $i . $desp2;
            }
            $href_str .= '<li>'.$page_href . $i . $end_fix . "\" " . $descriptions . $page_fix_str . ">$i</a></li>";
        }
        return $href_str;
    }
}
```