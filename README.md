# Contacts-FrameWork
//
//  Contacts.m
//  uicontrat
//
//  Created by huangzhiwu on 16/3/9.
//  Copyright © 2016年 huangzhiwu. All rights reserved.
//
#import <Contacts/Contacts.h>
#import "Contacts.h"
#import "PIContactInfo.h"

@implementation Contacts

- (void)loadAllPeopleInfo {
    
    CNContactStore *stroe = [[CNContactStore alloc]init];
    NSPredicate *predicate = nil;
    //提取电话，email、日期、URL 数据
    NSArray *contacts = [stroe unifiedContactsMatchingPredicate:predicate keysToFetch:
                         @[CNContactGivenNameKey,CNContactFamilyNameKey,
                           CNContactPhoneNumbersKey,
                           CNContactEmailAddressesKey,
                           CNContactDatesKey,
                           CNContactPostalAddressesKey,
                           CNContactUrlAddressesKey]
                                                          error:nil];
    
    if (contacts == NULL) {
        NSLog(@"%s, AddressBook is null. Return!", __FUNCTION__);
        return;
    }
    for (int i = 0; i < contacts.count; i++) {
        CNContact *person = contacts[i];
        PIContactInfo *info = [[PIContactInfo alloc] init];
        //获取姓名
        info.name = [[person.givenName stringByAppendingString:@" "]stringByAppendingString:person.familyName];
        //获取电话
        for (int j = 0; j < person.phoneNumbers.count; j ++) {
            CNLabeledValue *phone = [person.phoneNumbers objectAtIndex:j];
            //根据本地语言进行显示对于的label
            NSString *label =  [CNLabeledValue localizedStringForLabel:phone.label];
            label = [label stringByAppendingString:@": "];
            CNPhoneNumber *num  = phone.value;
            NSString *phone2 = [label stringByAppendingString:[num valueForKey:@"digits"]];
            [info addPhone:phone2];
        }
        //获取email
        for (int k = 0; k < person.emailAddresses.count; k ++) {
            CNLabeledValue *email = [person.emailAddresses objectAtIndex:k];
            NSString *value = email.value;
            NSString *label =  [CNLabeledValue localizedStringForLabel:email.label];
            label = [label stringByAppendingString:@": "];
            NSString *emali2 = [label stringByAppendingString:value];
            [info addEmail:emali2];
        }
        
        //获取URL
        for (int y = 0; y < person.urlAddresses.count; y ++) {
            CNLabeledValue *url = [person.urlAddresses objectAtIndex:y];
            NSString *label =  [CNLabeledValue localizedStringForLabel:url.label];
            label = [label stringByAppendingString:@": "];
            NSString *url2 = [label stringByAppendingString:url.value];
            [info addURL:url2];
        }
        
        //获取时间
        for (int x = 0; x < person.dates.count; x ++) {
            CNLabeledValue *dates = [person.dates objectAtIndex:x];
            NSString *label =  [CNLabeledValue localizedStringForLabel:dates.label];
            label = [label stringByAppendingString:@": "];
            NSDateComponents *date = dates.value;
            NSString *value = [NSString stringWithFormat:@"%ld-%02ld-%02ld",date.year,date.month,date.day];
            NSString *dates2 = [label stringByAppendingString:value];
            [info addDate:dates2];
        }
        
        [_persons addObject:info];
    }
}

@end
