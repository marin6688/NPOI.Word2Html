﻿# NPOI.Word2Html
Transfer word to HTML


        /// <summary>
        ///     上传docx文件转为html代码
        /// </summary>
        /// <param name="input"></param>
        /// <returns></returns>
        [HttpPost]
        public async Task<string> UploadDocx(UploadDocxInput input)
        {
            try
            {
                var file = input.File;
                if (file.ContentType.StartsWith("application/") &&
                    file.ContentType.IndexOf("word", StringComparison.Ordinal) > -1)
                {
                    var isImgUploadAliYun = input.IsImgUploadAliYun ?? false;
                    var stream = file.OpenReadStream();


                    NPOI.Word2Html.NpoiDoc npoiDocHelper = new NPOI.Word2Html.NpoiDoc();


                    return await npoiDocHelper.NpoiDocx(stream, isImgUploadAliYun ? NpoiDoc_uploadImgUrlyDelegate : (Func<byte[], string, string>)null);

                }

                throw new UserFriendlyException("请上传word2007以上版本文档");
            }
            catch (Exception e)
            {
                throw new UserFriendlyException(e.Message);
            }
        }
        private string NpoiDoc_uploadImgUrlyDelegate(byte[] imgByte, string picType)
        {
            //阿里云上传图片方法
            var url = _aliyunBinaryObjectManager.SaveAsync(new BinaryObject
            { Bytes = imgByte, FileName = Guid.NewGuid().ToString("N")+"."+ picType.Split('/')[1], FileType = picType }).Result;
            return url;
        }
